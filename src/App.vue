<template>
  <div class="dashboard">
    <!-- 头部区域 -->
    <header class="dashboard-header">
      <div class="header-left">
        <h1 class="dashboard-title">鹿鸣春舍智慧养老中心</h1>
        <nav class="header-nav">
          <a href="#" class="nav-item active">综合数据</a>
          <a href="#" class="nav-item">信息管理</a>
          <a href="#" class="nav-item">社区养老</a>
          <a href="#" class="nav-item">健康管理</a>
          <a href="#" class="nav-item">系统设置</a>
        </nav>
      </div>
      <!-- <div class="header-right">
        <div class="weather-info">
          <span class="weather-icon">{{ weatherInfo.icon }}</span>
          <span>{{ weatherInfo.text }} {{ temperature }}</span>
        </div>
        <div class="time-info">
          <span class="time">{{ currentTime }}</span>
          <span class="date">{{ currentDate }}</span>
        </div>
      </div> -->
    </header>

    <!-- 主体区域 -->
    <main class="dashboard-main">
      <section class="dashboard-top">
        <div class="top-content">
          <div class="top-left">
            <div class="info-row">
              <div class="time-display">
                <span class="time-text">{{ currentTime }}</span>
                <span class="date-text">{{ currentDate }}</span>
              </div>
              <div class="weather-info">
                <span class="weather-icon">{{ weatherInfo.icon }}</span>
                <div class="weather-details">
                  <span class="weather-text">{{ weatherInfo.text }} {{ temperature }}</span>
                  <span class="weather-wind">{{ weatherInfo.wind }}</span>
                </div>
              </div>
            </div>
          </div>
          <div class="top-center">
            <h2 class="section-title">智慧养老综合数据</h2>
          </div>
        </div>
      </section>
      
      <section class="dashboard-middle">
        <div class="middle-left">
          <div class="data-overview">
            <div class="total-elderly">
              <div class="total-label">养老人数总数</div>
              <div class="total-value">
                <span class="digit-box">1</span>
                <span class="comma">,</span>
                <span class="digit-box">2</span>
                <span class="digit-box">1</span>
                <span class="digit-box">0</span>
                <span class="comma">,</span>
                <span class="digit-box">0</span>
                <span class="digit-box">0</span>
                <span class="digit-box">0</span>
              </div>
            </div>
            <div class="age-groups">
              <div class="age-item">
                <div class="age-label">60-70岁老人</div>
                <div class="age-value">45,764</div>
              </div>
              <div class="age-item">
                <div class="age-label">71-80岁老人</div>
                <div class="age-value">45,764</div>
              </div>
              <div class="age-item">
                <div class="age-label">80岁以上老人</div>
                <div class="age-value">45,764</div>
              </div>
            </div>
            <div class="stats-grid">
              <div class="stats-item">
                <div class="stats-label">居家养老人数</div>
                <div class="stats-value">11,654</div>
              </div>
              <div class="stats-item">
                <div class="stats-label">社区居家养老人数</div>
                <div class="stats-value">234,456</div>
              </div>
              <div class="stats-item">
                <div class="stats-label">机构养老人数</div>
                <div class="stats-value">36,567</div>
              </div>
              <div class="stats-item">
                <div class="stats-label">养老机构数</div>
                <div class="stats-value">1,678</div>
              </div>
              <div class="stats-item">
                <div class="stats-label">服务人数</div>
                <div class="stats-value">34,345</div>
              </div>
              <div class="stats-item">
                <div class="stats-label">服务商数</div>
                <div class="stats-value">57,567</div>
              </div>
            </div>
          </div>
        </div>
        <div class="middle-center">
          <!-- <h3 class="panel-title">养老机构分布地图</h3> -->
          <div id="map" class="map-container">
          </div>
        </div>
        <div class="middle-right">
          <h3 class="panel-title">老年人年龄占比</h3>
          <div id="age-chart" class="age-chart">
          </div>
        </div>
      </section>
      
      <section class="dashboard-bottom">
        <div class="bottom-charts">
          <div class="chart-item">
            <h3 class="panel-title">居家养老分布</h3>
            <div id="home-care-chart" class="care-chart"></div>
          </div>
          <div class="chart-item">
            <h3 class="panel-title">社区养老分布</h3>
            <div id="community-care-chart" class="care-chart"></div>
          </div>
          <div class="chart-item">
            <h3 class="panel-title">机构养老分布</h3>
            <div id="institution-care-chart" class="care-chart"></div>
          </div>
        </div>
      </section>
    </main>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import * as echarts from 'echarts'
import chinaMapData from './assets/chengduMap.json'

// 时间日期相关
const currentTime = ref('')
const currentDate = ref('')
const temperature = ref('-2℃~0℃')
const weatherInfo = ref({
  icon: '❄️',
  text: '多云',
  wind: '北风2级 6m/s'
})

const updateDateTime = () => {
  const now = new Date()
  const year = now.getFullYear()
  const month = now.getMonth() + 1
  const day = now.getDate()
  const hours = String(now.getHours()).padStart(2, '0')
  const minutes = String(now.getMinutes()).padStart(2, '0')
  const seconds = String(now.getSeconds()).padStart(2, '0')
  
  currentTime.value = `${hours}:${minutes}:${seconds}`
  currentDate.value = `${year}年 ${month}月 ${day}日`
}

// 初始化地图
const initMap = () => {
  echarts.registerMap('china', chinaMapData)
  const mapChart = echarts.init(document.getElementById('map'))
  const option = {
    backgroundColor: 'transparent',
    geo: {
      map: 'china',
      roam: true,
      center: [104.06, 30.67],
      zoom: 10,
      scaleLimit: {
        min: 3,
        max: 30
      },
      boundingCoords: [
        [103.0, 30.0],
        [105.0, 31.5]
      ],
      label: {
        show: true,
        color: 'rgba(255, 255, 255, 0.85)',
        fontSize: 12,
        fontWeight: 500,
        textShadow: '0 2px 4px rgba(0,0,0,0.2)'
      },
      itemStyle: {
        areaColor: '#011B44',
        borderColor: 'rgba(24, 144, 255, 0.6)',
        borderWidth: 1.5,
        shadowColor: 'rgba(24, 144, 255, 0.2)',
        shadowBlur: 10
      },
      emphasis: {
        label: {
          color: '#fff',
          fontSize: 14,
          fontWeight: 'bold'
        },
        itemStyle: {
          areaColor: 'rgba(24, 144, 255, 0.4)',
          borderColor: '#1890ff',
          borderWidth: 2,
          shadowColor: 'rgba(24, 144, 255, 0.4)',
          shadowBlur: 20
        }
      },
      select: {
        itemStyle: {
          areaColor: 'rgba(24, 144, 255, 0.5)',
          borderColor: '#1890ff',
          borderWidth: 2
        }
      }
    },
    series: [{
      type: 'effectScatter',
      coordinateSystem: 'geo',
      data: [
        {name: '成华区养老中心', value: [104.101, 30.660, 20], symbol: 'circle'},
        {name: '武侯区颐养院', value: [104.043, 30.641, 18], symbol: 'circle'},
        {name: '锦江区养老院', value: [104.080, 30.657, 15], symbol: 'circle'},
        {name: '青羊区养老中心', value: [104.062, 30.674, 16], symbol: 'circle'},
        {name: '金牛区颐养院', value: [104.052, 30.691, 17], symbol: 'circle'},
        {name: '高新区养老院', value: [104.065, 30.621, 14], symbol: 'circle'},
        {name: '温江区养老中心', value: [103.856, 30.682, 12], symbol: 'circle'},
        {name: '双流区颐养院', value: [103.923, 30.574, 13], symbol: 'circle'},
        {name: '新都区养老院', value: [104.158, 30.823, 16], symbol: 'circle'},
        {name: '郫都区颐养院', value: [103.901, 30.795, 15], symbol: 'circle'},
        {name: '龙泉驿区养老中心', value: [104.274, 30.556, 14], symbol: 'circle'},
        {name: '青白江区养老院', value: [104.251, 30.878, 13], symbol: 'circle'},
        {name: '简阳市养老中心', value: [104.547, 30.411, 12], symbol: 'circle'},
        {name: '都江堰市养老院', value: [103.647, 30.988, 11], symbol: 'circle'},
        {name: '彭州市颐养院', value: [103.958, 30.990, 10], symbol: 'circle'},
        {name: '邛崃市养老中心', value: [103.464, 30.410, 9], symbol: 'circle'}
      ],
      symbolSize: 14,
      showEffectOn: 'render',
      rippleEffect: {
        period: 3,
        scale: 4,
        brushType: 'fill'
      },
      hoverAnimation: true,
      label: {
        show: true,
        position: 'right',
        formatter: '{b}',
        color: '#fff',
        fontSize: 12,
        textShadow: '0 2px 4px rgba(0,0,0,0.3)'
      },
      itemStyle: {
        color: '#1890ff',
        shadowBlur: 15,
        shadowColor: 'rgba(24, 144, 255, 0.6)',
        borderColor: '#fff',
        borderWidth: 2
      },
      emphasis: {
        scale: true,
        label: {
          show: true,
          color: '#fff',
          fontSize: 14,
          fontWeight: 'bold',
          textShadow: '0 2px 4px rgba(0,0,0,0.5)'
        },
        itemStyle: {
          color: '#36cfc9',
          shadowBlur: 20,
          shadowColor: 'rgba(54, 207, 201, 0.6)'
        }
      }
    }]
  }
  mapChart.setOption(option)
}

// 初始化年龄占比图
const initCareCharts = () => {
  // 居家养老分布图表
  const homeCareChart = echarts.init(document.getElementById('home-care-chart'))
  const homeCareOption = {
    backgroundColor: 'transparent',
    tooltip: {
      trigger: 'item',
      backgroundColor: 'rgba(0, 21, 41, 0.9)',
      borderColor: 'rgba(24, 144, 255, 0.3)',
      textStyle: { color: '#fff' }
    },
    legend: {
      orient: 'horizontal',
      bottom: '0',
      left: 'center',
      itemWidth: 10,
      itemHeight: 10,
      itemGap: 20,
      textStyle: { 
        color: 'rgba(255, 255, 255, 0.85)',
        fontSize: 12
      }
    },
    color: ['#FF6B6B', '#4ECDC4', '#45B7D1', '#96CDEF'],
    series: [{
      name: '居家养老',
      type: 'pie',
      radius: ['50%', '70%'],
      center: ['40%', '50%'],
      avoidLabelOverlap: false,
      label: {
        show: true,
        position: 'inside',
        formatter: '{d}%',
        color: '#fff',
        fontSize: 12,
        fontWeight: 'bold'
      },
      labelLine: {
        show: true,
        lineStyle: { color: 'rgba(255,255,255,0.3)' }
      },
      data: [
        {value: 4500, name: '专人照护'},
        {value: 3200, name: '居家服务'},
        {value: 2100, name: '远程看护'},
        {value: 1854, name: '其他服务'}
      ]
    }]
  }
  homeCareChart.setOption(homeCareOption)

  // 社区养老分布图表
  const communityCareChart = echarts.init(document.getElementById('community-care-chart'))
  const communityCareOption = {
    backgroundColor: 'transparent',
    tooltip: {
      trigger: 'item',
      backgroundColor: 'rgba(0, 21, 41, 0.9)',
      borderColor: 'rgba(24, 144, 255, 0.3)',
      textStyle: { color: '#fff' }
    },
    legend: {
      orient: 'horizontal',
      bottom: '0',
      left: 'center',
      itemWidth: 10,
      itemHeight: 10,
      itemGap: 20,
      textStyle: { 
        color: 'rgba(255, 255, 255, 0.85)',
        fontSize: 12
      }
    },
    color: ['#36CFC9', '#73D13D', '#FFC53D', '#FF7A45'],
    series: [{
      name: '社区养老',
      type: 'pie',
      radius: ['50%', '70%'],
      center: ['40%', '50%'],
      avoidLabelOverlap: false,
      label: {
        show: true,
        position: 'inside',
        formatter: '{d}%',
        color: '#fff',
        fontSize: 12,
        fontWeight: 'bold'
      },
      labelLine: {
        show: true,
        lineStyle: { color: 'rgba(255,255,255,0.3)' }
      },
      data: [
        {value: 95000, name: '日间照料'},
        {value: 68000, name: '文娱活动'},
        {value: 45000, name: '健康管理'},
        {value: 26456, name: '其他服务'}
      ]
    }]
  }
  communityCareChart.setOption(communityCareOption)

  // 机构养老分布图表
  const institutionCareChart = echarts.init(document.getElementById('institution-care-chart'))
  const institutionCareOption = {
    backgroundColor: 'transparent',
    tooltip: {
      trigger: 'item',
      backgroundColor: 'rgba(0, 21, 41, 0.9)',
      borderColor: 'rgba(24, 144, 255, 0.3)',
      textStyle: { color: '#fff' }
    },
    legend: {
      orient: 'horizontal',
      bottom: '0',
      left: 'center',
      itemWidth: 10,
      itemHeight: 10,
      itemGap: 20,
      textStyle: { 
        color: 'rgba(255, 255, 255, 0.85)',
        fontSize: 12
      }
    },
    color: ['#597EF7', '#9254DE', '#F759AB', '#FF4D4F'],
    series: [{
      name: '机构养老',
      type: 'pie',
      radius: ['50%', '70%'],
      center: ['40%', '50%'],
      avoidLabelOverlap: false,
      label: {
        show: true,
        position: 'inside',
        formatter: '{d}%',
        color: '#fff',
        fontSize: 12,
        fontWeight: 'bold'
      },
      labelLine: {
        show: true,
        lineStyle: { color: 'rgba(255,255,255,0.3)' }
      },
      data: [
        {value: 15000, name: '全托养老'},
        {value: 12000, name: '医养结合'},
        {value: 8000, name: '专护养老'},
        {value: 1567, name: '其他服务'}
      ]
    }]
  }
  institutionCareChart.setOption(institutionCareOption)
}

const initAgeChart = () => {
  const ageChart = echarts.init(document.getElementById('age-chart'))
  const option = {
    backgroundColor: 'transparent',
    tooltip: {
      trigger: 'item',
      backgroundColor: 'rgba(0, 21, 41, 0.9)',
      borderColor: 'rgba(24, 144, 255, 0.3)',
      textStyle: {
        color: '#fff'
      }
    },
    legend: {
      orient: 'vertical',
      right: '40%',
      top: 'center',
      itemWidth: 10,
      itemHeight: 10,
      textStyle: {
        color: 'rgba(255, 255, 255, 0.85)',
        fontSize: 12
      },
      itemStyle: {
        borderWidth: 0
      }
    },
    color: ['#FF6B6B', '#4ECDC4', '#45B7D1', '#96CDEF'],
    series: [{
      name: '年龄占比',
      type: 'pie',
      radius: ['50%', '70%'],
      avoidLabelOverlap: false,
      label: {
        show: true,
        position: 'inside',
        formatter: '{d}%',
        color: '#fff',
        fontSize: 12,
        fontWeight: 'bold'
      },
      labelLine: {
        show: true,
        lineStyle: {
          color: 'rgba(255,255,255,0.3)'
        }
      },
      data: [
        {value: 35, name: '60-69岁'},
        {value: 30, name: '70-79岁'},
        {value: 25, name: '80-89岁'},
        {value: 10, name: '90岁以上'}
      ]
    }]
  }
  ageChart.setOption(option)
}

onMounted(() => {
  updateDateTime()
  setInterval(updateDateTime, 1000)
  initMap()
  initAgeChart()
  initCareCharts()
})
</script>

<style scoped>
.dashboard {
  min-height: 100vh;
  width: 100vw;
  margin: 0;
  padding: 0;
  background-color: #000B1C;
  color: #fff;
  background-image: radial-gradient(circle at 50% 50%, rgba(24, 144, 255, 0.1) 0%, transparent 80%);
  display: flex;
  flex-direction: column;
  overflow: hidden;
  position: absolute;
  top: 0;
  left: 0;
}

.dashboard-header {
  height: 60px;
  padding: 0 20px;
  background: #001529;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.header-left {
  display: flex;
  align-items: center;
  gap: 32px;
}

.dashboard-title {
  font-size: 20px;
  font-weight: 500;
  color: #fff;
  margin: 0;
  white-space: nowrap;
}

.header-nav {
  display: flex;
  gap: 24px;
}

.nav-item {
  color: rgba(255, 255, 255, 0.65);
  text-decoration: none;
  font-size: 14px;
  padding: 0 4px;
  position: relative;
  transition: color 0.3s;
}

.nav-item:hover,
.nav-item.active {
  color: #fff;
}

.nav-item.active::after {
  content: '';
  position: absolute;
  bottom: -21px;
  left: 0;
  width: 100%;
  height: 2px;
  background: #1890FF;
}

.header-right {
  display: flex;
  align-items: center;
}

.time-info {
  display: flex;
  flex-direction: row-reverse;
  align-items: center;
  gap: 16px;
  color: rgba(255, 255, 255, 0.65);
  font-size: 14px;
}

.weather-info,
.time-info {
  display: flex;
  align-items: center;
  gap: 8px;
  color: #A3AED0;
  font-size: 14px;
}

.weather-icon {
  font-size: 20px;
}

.time-info {
  border-left: 1px solid rgba(24, 144, 255, 0.2);
  padding-left: 24px;
}

.date {
  margin-right: 12px;
}

.dashboard-main {
  flex: 1;
  padding: 20px;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.dashboard-top {
  /* min-height: 120px; */
  background: rgba(1, 27, 68, 0.7);
  border-radius: 8px;
  padding: 8px;
  border: 1px solid rgba(24, 144, 255, 0.3);
}

.top-content {
  display: flex;
  align-items: center;
  position: relative;
  height: 40px;
  justify-content: center;
}

.info-row {
  display: flex;
  align-items: center;
  gap: 24px;
  padding: 8px 16px;
}

.time-text {
  color: #fff;
  font-size: 20px;
  font-weight: 500;
}

.weather-icon {
  font-size: 28px;
}

.weather-details {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.top-left {
  position: absolute;
  left: 0;
}

.top-center {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
}

.section-title {
  color: #fff;
  font-size: 24px;
  font-weight: 600;
  margin: 0;
  text-align: center;
  text-shadow: 0 0 10px rgba(24, 144, 255, 0.5),
               0 0 20px rgba(24, 144, 255, 0.3),
               0 0 30px rgba(24, 144, 255, 0.1);
  position: relative;
  padding: 0 20px;
}

.section-title::before,
.section-title::after {
  content: '';
  position: absolute;
  top: 50%;
  width: 30px;
  height: 2px;
  background: linear-gradient(90deg, rgba(24, 144, 255, 0.8), transparent);
  transform: translateY(-50%);
}

.section-title::before {
  right: 100%;
}

.section-title::after {
  left: 100%;
  transform: translateY(-50%) rotate(180deg);
}

.info-row {
  display: flex;
  align-items: center;
  gap: 24px;
  padding: 8px 16px;
}

.time-display {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.time-text {
  color: #fff;
  font-size: 24px;
  font-weight: 500;
}

.date-text {
  color: rgba(255, 255, 255, 0.85);
  font-size: 14px;
}

.weather-info {
  display: flex;
  align-items: center;
  gap: 12px;
  border-left: 1px solid rgba(24, 144, 255, 0.2);
  padding-left: 24px;
}

.weather-icon {
  font-size: 32px;
}

.weather-details {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.weather-text {
  color: #fff;
  font-size: 16px;
}

.weather-wind {
  color: rgba(255, 255, 255, 0.85);
  font-size: 14px;
}

.top-center {
  position: static;
  transform: none;
}

.section-title {
  color: #fff;
  font-size: 20px;
  font-weight: 500;
  margin: 0;
  text-align: center;
}

.dashboard-middle {
  flex: 1;
  min-height: 400px;
  background: rgba(1, 27, 68, 0.7);
  border-radius: 8px;
  padding: 16px;
  display: flex;
  gap: 16px;
}

.middle-left,
.middle-center,
.middle-right {
  background: rgba(1, 27, 68, 0.5);
  border-radius: 4px;
  padding: 16px;
  border: 1px solid rgba(24, 144, 255, 0.2);
}

.middle-left {
  width: 25%;
}

.middle-center {
  width: 50%;
}

.middle-right {
  width: 25%;
}

.panel-title {
  color: #fff;
  font-size: 16px;
  font-weight: 500;
  margin: 0 0 12px 0;
  padding-left: 12px;
  border-left: 3px solid #1890FF;
}

.data-overview {
  display: flex;
  flex-direction: column;
  gap: 16px;
  padding: 12px;
}

.total-elderly {
  text-align: center;
  margin-bottom: 4px;
}

.total-label {
  color: rgba(255, 255, 255, 0.85);
  font-size: 14px;
  margin-bottom: 12px;
}

.age-groups {
  display: flex;
  flex-direction: column;
  gap: 8px;
  padding: 12px 0;
  border-top: 1px solid rgba(24, 144, 255, 0.2);
  border-bottom: 1px solid rgba(24, 144, 255, 0.2);
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 12px 16px;
}

.stats-item {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.stats-label {
  color: rgba(255, 255, 255, 0.65);
  font-size: 13px;
}

.stats-value {
  color: #fff;
  font-size: 16px;
  font-weight: 500;
}

.map-container,
.age-chart {
  height: calc(100% - 20px);
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: rgba(255, 255, 255, 0.45);
}

.dashboard-bottom {
  min-height: 300px;
  background: rgba(1, 27, 68, 0.7);
  border-radius: 8px;
  padding: 12px;
  border: 1px solid rgba(24, 144, 255, 0.3);
}

.top-content {
  display: flex;
  align-items: center;
  position: relative;
  height: 48px;
  justify-content: center;
}

.info-row {
  display: flex;
  align-items: center;
  gap: 24px;
  padding: 8px 16px;
}

.time-text {
  color: #fff;
  font-size: 20px;
  font-weight: 500;
}

.weather-icon {
  font-size: 24px;
}

.weather-details {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.top-left {
  position: absolute;
  left: 0;
}

.top-center {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
}

.section-title {
  color: #fff;
  font-size: 20px;
  font-weight: 500;
  margin: 0;
  text-align: center;
}

.dashboard-middle {
  flex: 1;
  min-height: 400px;
  background: rgba(1, 27, 68, 0.7);
  border-radius: 8px;
  padding: 16px;
  display: flex;
  gap: 16px;
}

.middle-left,
.middle-center,
.middle-right {
  background: rgba(1, 27, 68, 0.5);
  border-radius: 4px;
  padding: 16px;
  border: 1px solid rgba(24, 144, 255, 0.2);
}



.panel-title {
  color: #fff;
  font-size: 16px;
  font-weight: 500;
  margin: 0 0 16px 0;
  padding-left: 12px;
  border-left: 3px solid #1890FF;
}

.data-overview {
  display: flex;
  flex-direction: column;
  gap: 24px;
  padding: 16px;
}

.total-elderly {
  text-align: center;
  margin-bottom: 8px;
}

.total-label {
  color: rgba(255, 255, 255, 0.85);
  font-size: 16px;
  margin-bottom: 16px;
}

.total-value {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 4px;
}

.digit-box {
  background: rgba(24, 144, 255, 0.2);
  border: 1px solid rgba(24, 144, 255, 0.3);
  color: #fff;
  font-size: 28px;
  font-weight: 500;
  width: 36px;
  height: 48px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 4px;
}

.comma {
  color: #fff;
  font-size: 28px;
  font-weight: 500;
  margin: 0 -2px;
}

.age-groups {
  display: flex;
  flex-direction: column;
  gap: 12px;
  padding: 16px 0;
  border-top: 1px solid rgba(24, 144, 255, 0.2);
  border-bottom: 1px solid rgba(24, 144, 255, 0.2);
}

.age-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.age-label {
  color: rgba(255, 255, 255, 0.85);
  font-size: 14px;
}

.age-value {
  color: #fff;
  font-size: 16px;
  font-weight: 500;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 16px 24px;
}

.stats-item {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.stats-label {
  color: rgba(255, 255, 255, 0.65);
  font-size: 13px;
}

.stats-value {
  color: #fff;
  font-size: 16px;
  font-weight: 500;
}

.map-container,
.age-chart {
  height: calc(100% - 20px);
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: rgba(255, 255, 255, 0.45);
}

.dashboard-bottom {
  height: 300px;
  background: rgba(1, 27, 68, 0.7);
  border-radius: 8px;
  padding: 8px;
  border: 1px solid rgba(24, 144, 255, 0.3);
}

.bottom-charts {
  display: flex;
  gap: 12px;
  height: 100%;
}

.chart-item {
  flex: 1;
  background: rgba(1, 27, 68, 0.5);
  border-radius: 4px;
  padding: 12px;
  border: 1px solid rgba(24, 144, 255, 0.2);
}

.care-chart {
  height: calc(100% - 32px);
  width: 100%;
}
</style>
