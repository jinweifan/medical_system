<template>
  <div class="dashboard">
    <!-- 头部区域 -->
    <header class="dashboard-header">
      <div class="header-left">
        <h1 class="dashboard-title">鹿鸣春舍智慧养老中心</h1>
        <nav class="header-nav">
          <a href="#" class="nav-item active">系统管理</a>
          <a href="#" class="nav-item">信息管理</a>
          <a href="#" class="nav-item">膳食管理</a>
          <a href="#" class="nav-item">护理管理</a>
          <a href="#" class="nav-item">床位管理</a>
          <a href="#" class="nav-item">后勤管理</a>
          <a href="#" class="nav-item">费用管理</a>
        </nav>
      </div>
    </header>

    <!-- 主体区域 -->
    <main class="dashboard-main">
      <section class="dashboard-top">
        <div class="top-content">
          <div class="top-left">
            <div class="info-row">
              <span class="datetime-text">{{ currentDate }} {{ currentTime }}</span>
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
                <span class="digit-box">0</span>
                <span class="digit-box">2</span>
                <span class="digit-box">3</span>
                <span class="digit-box">0</span>
              </div>
            </div>
            <div class="age-groups">
              <div class="age-item">
                <div class="age-label">60-70岁老人</div>
                <div class="age-value">
                  <span class="digit-box">0</span>
                  <span class="digit-box">5</span>
                  <span class="digit-box">6</span>
                </div>
              </div>
              <div class="age-item">
                <div class="age-label">71-80岁老人</div>
                <div class="age-value">
                  <span class="digit-box">0</span>
                  <span class="digit-box">8</span>
                  <span class="digit-box">2</span>
                </div>
              </div>
              <div class="age-item">
                <div class="age-label">80岁以上老人</div>
                <div class="age-value">
                  <span class="digit-box">0</span>
                  <span class="digit-box">4</span>
                  <span class="digit-box">9</span>
                </div>
              </div>
            </div>
            <div class="stats-grid">
              <div class="stats-item">
                <div class="stats-label">今日入住人数</div>
                <div class="stats-value">11</div>
              </div>
              <div class="stats-item">
                <div class="stats-label">今日离开人数</div>
                <div class="stats-value">8</div>
              </div>
              <div class="stats-item">
                <div class="stats-label">常驻床位数量</div>
                <div class="stats-value">204</div>
              </div>
              <div class="stats-item">
                <div class="stats-label">剩余床位数量</div>
                <div class="stats-value">180</div>
              </div>
              <div class="stats-item">
                <div class="stats-label">护理服务人数</div>
                <div class="stats-value">75</div>
              </div>
              <div class="stats-item">
                <div class="stats-label">续费提醒</div>
                <div class="stats-value">16</div>
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
          <div id="age-chart" class="pie-chart">
          </div>
        </div>
      </section>

      <section class="dashboard-bottom">
          <div class="bottom-left">
            <h3 class="panel-title">本月护理项目占比</h3>
            <div id="home-care-chart" class="care-chart"></div>
          </div>
          <div class="bottom-center">
            <h3 class="panel-title">客户入住趋势分析</h3>
            <div id="community-care-chart" class="care-chart"></div>
          </div>
          <div class="bottom-right">
            <h3 class="panel-title">健康统计</h3>
            <div id="institution-care-chart" class="care-chart"></div>
          </div>
      </section>
    </main>
  </div>
</template>

<script setup>
import { ref, onMounted, nextTick } from 'vue'
import * as echarts from 'echarts'
import chinaMapData from './assets/chengduMap.json'

// 时间日期相关
const currentTime = ref('')
const currentDate = ref('')

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
  const mapChart = echarts.init(document.getElementById('map'))
  echarts.registerMap('china', chinaMapData)
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
        { name: '成华区养老中心', value: [104.101, 30.660, 20], symbol: 'circle' },
        { name: '武侯区颐养院', value: [104.043, 30.641, 18], symbol: 'circle' },
        { name: '锦江区养老院', value: [104.080, 30.657, 15], symbol: 'circle' },
        { name: '青羊区养老中心', value: [104.062, 30.674, 16], symbol: 'circle' },
        { name: '金牛区颐养院', value: [104.052, 30.691, 17], symbol: 'circle' },
        { name: '高新区养老院', value: [104.065, 30.621, 14], symbol: 'circle' },
        { name: '温江区养老中心', value: [103.856, 30.682, 12], symbol: 'circle' },
        { name: '双流区颐养院', value: [103.923, 30.574, 13], symbol: 'circle' },
        { name: '新都区养老院', value: [104.158, 30.823, 16], symbol: 'circle' },
        { name: '郫都区颐养院', value: [103.901, 30.795, 15], symbol: 'circle' },
        { name: '龙泉驿区养老中心', value: [104.274, 30.556, 14], symbol: 'circle' },
        { name: '青白江区养老院', value: [104.251, 30.878, 13], symbol: 'circle' },
        { name: '简阳市养老中心', value: [104.547, 30.411, 12], symbol: 'circle' },
        { name: '都江堰市养老院', value: [103.647, 30.988, 11], symbol: 'circle' },
        { name: '彭州市颐养院', value: [103.958, 30.990, 10], symbol: 'circle' },
        { name: '邛崃市养老中心', value: [103.464, 30.410, 9], symbol: 'circle' }
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
const initAgeChart = () => {
  const ageChart = echarts.init(document.getElementById('age-chart'))
  const option = {
    backgroundColor: 'transparent',
    tooltip: {
      trigger: 'item',
      formatter: '{b}: {d}%',
      backgroundColor: 'rgba(0, 21, 41, 0.9)',
      borderColor: 'rgba(24, 144, 255, 0.3)',
      textStyle: { color: '#fff' }
    },
    legend: {
      orient: 'horizontal',
      bottom: '0',
      left: 'center',
      itemGap: 15,
      itemWidth: 10,
      itemHeight: 10,
      textStyle: {
        color: 'rgba(255, 255, 255, 0.65)',
        fontSize: 14
      }
    },
    color: ['#FF6B6B', '#FFD93D', '#4ECDC4', '#45B7D1', '#6C5CE7'],
    series: [{
      name: '年龄占比',
      type: 'pie',
      radius: ['40%', '60%'],
      center: ['50%', '42%'],
      avoidLabelOverlap: true,
      itemStyle: {
        borderColor: 'rgba(0, 21, 41, 0.4)',
        borderWidth: 2
      },
      label: {
        show: true,
        position: 'outside',
        formatter: '{b}\n{d}%',
        fontSize: 12,
        color: '#fff',
        lineHeight: 16,
        alignTo: 'edge',
        edgeDistance: '15%',
        distanceToLabelLine: 10,
        overflow: 'none'
      },
      labelLayout: {
        hideOverlap: false,
        moveOverlap: 'shiftY'
      },
      labelLine: {
        show: true,
        length: 25,
        length2: 50,
        minTurnAngle: 90,
        smooth: 0.2,
        lineStyle: {
          width: 1.5,
          type: 'solid'
        }
      },
      data: [
        { 
          value: 25, 
          name: '60-69岁',
          itemStyle: { color: '#FF6B6B' },
          labelLine: { lineStyle: { color: '#FF6B6B' } }
        },
        { 
          value: 40, 
          name: '70-79岁',
          itemStyle: { color: '#FFD93D' },
          labelLine: { lineStyle: { color: '#FFD93D' } }
        },
        { 
          value: 18, 
          name: '80-89岁',
          itemStyle: { color: '#4ECDC4' },
          labelLine: { lineStyle: { color: '#4ECDC4' } }
        },
        { 
          value: 10, 
          name: '90-99岁',
          itemStyle: { color: '#45B7D1' },
          labelLine: { lineStyle: { color: '#45B7D1' } }
        },
        { 
          value: 7, 
          name: '100岁以上',
          itemStyle: { color: '#6C5CE7' },
          labelLine: { lineStyle: { color: '#6C5CE7' } }
        }
      ]
    }]
  }
  ageChart.setOption(option)
}

// 初始化养老分布图表
const initCareCharts = () => {
  // 护理项目占比
  const homeCareChart = echarts.init(document.getElementById('home-care-chart'))
  const homeCareOption = {
    backgroundColor: 'transparent',
    tooltip: {
      trigger: 'item',
      formatter: '{b}: {d}%',
      backgroundColor: 'rgba(0, 21, 41, 0.9)',
      borderColor: 'rgba(24, 144, 255, 0.3)',
      textStyle: { color: '#fff' }
    },
    legend: {
      orient: 'horizontal',
      bottom: '0',
      left: 'center',
      itemGap: 15,
      itemWidth: 15,
      itemHeight: 10,
      textStyle: {
        color: 'rgba(255, 255, 255, 0.65)',
        fontSize: 14
      }
    },
    color: ['#FF9F43', '#FFD93D', '#4ECDC4', '#45B7D1', '#6C5CE7', '#FF6B6B'],
    series: [{
      name: '护理项目',
      type: 'pie',
      radius: ['45%', '60%'],
      center: ['50%', '42%'],
      avoidLabelOverlap: true,
      itemStyle: {
        borderColor: 'rgba(0, 21, 41, 0.4)',
        borderWidth: 2
      },
      label: {
        show: true,
        position: 'outside',
        formatter: '{b}\n{d}%',
        fontSize: 12,
        color: '#fff',
        lineHeight: 16,
        alignTo: 'edge',
        edgeDistance: '15%',
        distanceToLabelLine: 10,
        overflow: 'none'
      },
      labelLayout: {
        hideOverlap: false,
        moveOverlap: 'shiftY'
      },
      labelLine: {
        show: true,
        length: 25,
        length2: 50,
        minTurnAngle: 90,
        smooth: 0.2,
        lineStyle: {
          width: 1.5,
          type: 'solid'
        }
      },
      data: [
        { 
          value: 20, 
          name: '饮食',
          itemStyle: { color: '#FF9F43' },
          labelLine: { lineStyle: { color: '#FF9F43' } }
        },
        { 
          value: 20, 
          name: '康复',
          itemStyle: { color: '#FFD93D' },
          labelLine: { lineStyle: { color: '#FFD93D' } }
        },
        { 
          value: 10, 
          name: '助行',
          itemStyle: { color: '#4ECDC4' },
          labelLine: { lineStyle: { color: '#4ECDC4' } }
        },
        { 
          value: 15, 
          name: '药品',
          itemStyle: { color: '#45B7D1' },
          labelLine: { lineStyle: { color: '#45B7D1' } }
        },
        { 
          value: 20, 
          name: '清洁',
          itemStyle: { color: '#6C5CE7' },
          labelLine: { lineStyle: { color: '#6C5CE7' } }
        },
        { 
          value: 15, 
          name: '医疗',
          itemStyle: { color: '#FF6B6B' },
          labelLine: { lineStyle: { color: '#FF6B6B' } }
        }
      ]
    }]
  }
  homeCareChart.setOption(homeCareOption)

  // 入住趋势分析
  const communityCareChart = echarts.init(document.getElementById('community-care-chart'))
  const communityCareOption = {
    backgroundColor: 'transparent',
    tooltip: {
      trigger: 'axis',
      axisPointer: {
        type: 'shadow'
      },
      backgroundColor: 'rgba(0, 21, 41, 0.9)',
      borderColor: 'rgba(24, 144, 255, 0.3)',
      textStyle: { color: '#fff' }
    },
    grid: {
      top: '3%',
      left: '8%',
      right: '4%',
      bottom: '3%',
      containLabel: true
    },
    yAxis: {
      type: 'category',
      data: ['1月', '2月', '3月', '4月', '5月'],
      axisLine: {
        show: true,
        lineStyle: { color: 'rgba(255, 255, 255, 0.3)' }
      },
      axisTick: {
        show: false
      },
      axisLabel: {
        color: 'rgba(255, 255, 255, 0.65)',
        fontSize: 12,
        margin: 16
      }
    },
    xAxis: {
      type: 'value',
      max: 250,
      axisLine: {
        show: true,
        lineStyle: { color: 'rgba(255, 255, 255, 0.3)' }
      },
      splitLine: {
        show: true,
        lineStyle: { 
          color: 'rgba(255, 255, 255, 0.1)',
          type: 'dashed'
        }
      },
      axisLabel: {
        color: 'rgba(255, 255, 255, 0.65)',
        fontSize: 12
      }
    },
    series: [
      {
        name: '新增入住',
        type: 'bar',
        barWidth: 12,
        barGap: '0%',
        barCategoryGap: '50%',
        itemStyle: {
          color: '#FF6B6B',
          borderRadius: [0, 4, 4, 0]
        },
        data: [10, 20, 40, 25, 30]
      },
      {
        name: '离开人数',
        type: 'bar',
        barWidth: 12,
        barGap: '0%',
        barCategoryGap: '50%',
        itemStyle: {
          color: '#FFD93D',
          borderRadius: [0, 4, 4, 0]
        },
        data: [35, 30, 20, 35, 40]
      },
      {
        name: '现有人数',
        type: 'bar',
        barWidth: 12,
        barGap: '0%',
        barCategoryGap: '50%',
        itemStyle: {
          color: '#4ECDC4',
          borderRadius: [0, 4, 4, 0]
        },
        data: [235, 225, 245, 240, 230]
      }
    ]
  }
  communityCareChart.setOption(communityCareOption)

  // 健康状况分析
  const institutionCareChart = echarts.init(document.getElementById('institution-care-chart'))
  const institutionCareOption = {
    backgroundColor: 'transparent',
    tooltip: {
      trigger: 'item',
      formatter: '{b}: {d}%',
      backgroundColor: 'rgba(0, 21, 41, 0.9)',
      borderColor: 'rgba(24, 144, 255, 0.3)',
      textStyle: { color: '#fff' }
    },
    legend: {
      orient: 'horizontal',
      bottom: '0',
      left: 'center',
      itemGap: 15,
      itemWidth: 15,
      itemHeight: 10,
      textStyle: {
        color: 'rgba(255, 255, 255, 0.65)',
        fontSize: 14
      }
    },
    color: ['#4ECDC4', '#FFD93D', '#45B7D1', '#FFB156', '#FF6B6B'],
    series: [{
      type: 'pie',
      roseType: 'radius',
      radius: ['20%', '55%'],
      center: ['50%', '42%'],
      itemStyle: {
        borderColor: 'rgba(0, 21, 41, 0.4)',
        borderWidth: 2
      },
      label: {
        show: true,
        position: 'outside',
        formatter: '{b}\n{d}%',
        fontSize: 12,
        color: '#fff',
        lineHeight: 16,
        alignTo: 'edge',
        edgeDistance: '15%',
        distanceToLabelLine: 10,
        overflow: 'none'
      },
      labelLayout: {
        hideOverlap: false,
        moveOverlap: 'shiftY'
      },
      labelLine: {
        show: true,
        length: 25,
        length2: 50,
        minTurnAngle: 90,
        smooth: 0.2,
        lineStyle: {
          width: 1.5,
          type: 'solid'
        }
      },
      data: [
        { 
          value: 30, 
          name: '高血压',
          itemStyle: { color: '#4ECDC4' },
          labelLine: { lineStyle: { color: '#4ECDC4' } }
        },
        { 
          value: 25, 
          name: '糖尿病',
          itemStyle: { color: '#FFD93D' },
          labelLine: { lineStyle: { color: '#FFD93D' } }
        },
        { 
          value: 20, 
          name: '冠心病',
          itemStyle: { color: '#45B7D1' },
          labelLine: { lineStyle: { color: '#45B7D1' } }
        },
        { 
          value: 15, 
          name: '心机衰竭',
          itemStyle: { color: '#FFB156' },
          labelLine: { lineStyle: { color: '#FFB156' } }
        },
        { 
          value: 10, 
          name: '心绞痛',
          itemStyle: { color: '#FF6B6B' },
          labelLine: { lineStyle: { color: '#FF6B6B' } }
        }
      ],
      emphasis: {
        scale: true,
        scaleSize: 10,
        itemStyle: {
          shadowBlur: 10,
          shadowColor: 'rgba(0, 0, 0, 0.5)'
        },
        labelLine: {
          lineStyle: {
            width: 3
          }
        }
      }
    }]
  }
  institutionCareChart.setOption(institutionCareOption)
}

onMounted(() => {
  updateDateTime()
  setInterval(updateDateTime, 1000)

  // 确保DOM完全加载后再初始化图表
  nextTick(() => {
    try {
      // 初始化地图
      const mapChart = echarts.init(document.getElementById('map'))
      if (mapChart) {
        initMap()
        console.log('Map chart initialized')
      }

      // 初始化年龄分布图
      const ageChart = echarts.init(document.getElementById('age-chart'))
      if (ageChart) {
        initAgeChart()
        console.log('Age chart initialized')
      }

      // 初始化底部三个图表
      const homeCareChart = echarts.init(document.getElementById('home-care-chart'))
      const communityCareChart = echarts.init(document.getElementById('community-care-chart'))
      const institutionCareChart = echarts.init(document.getElementById('institution-care-chart'))

      if (homeCareChart && communityCareChart && institutionCareChart) {
        initCareCharts()
        console.log('Care charts initialized')
      }

      // 监听窗口大小变化
      window.addEventListener('resize', () => {
        mapChart?.resize()
        ageChart?.resize()
        homeCareChart?.resize()
        communityCareChart?.resize()
        institutionCareChart?.resize()
      })
    } catch (error) {
      console.error('Error initializing charts:', error)
    }
  })
})
</script>

<style scoped>
.dashboard {
  width: 100vw;
  height: 100vh;
  margin: 0;
  padding: 0;
  color: #fff;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.dashboard-header {
  height: 60px;
  padding: 0 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-shrink: 0;
}

.header-left {
  display: flex;
  align-items: center;
  gap: 32px;
}

.dashboard-title {
  font-size: 1.25rem;
}

.header-nav {
  display: flex;
  gap: 24px;
}

.nav-item {
  color: rgba(255, 255, 255, 0.65);
  text-decoration: none;
  font-size: 0.875rem;
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
}

.header-right {
  display: flex;
  align-items: center;
}

.time-info {
  display: flex;
  flex-direction: row-reverse;
  align-items: center;
  gap: 20px;
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
  display: flex;
  flex-direction: column;
  gap: 20px;
  padding: 20px;
  min-height: 0;
}

.dashboard-top {
  height: 60px;
  border-radius: 8px;
  padding: 8px;
  background-image: url('src/assets/images/head_bg.png');
  background-size: cover;
  background-position: center;
  flex-shrink: 0;
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
  gap: 16px;
  height: 100%;
  padding: 8px 16px;
}

.datetime-text {
  color: #fff;
  font-size: 25px;
  font-weight: 500;
}

.top-left {
  position: absolute;
   height: 100%;
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
  font-size: 2rem;
  font-weight: 400;
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
  transform: translateY(-50%);
}

.dashboard-middle,
.dashboard-bottom {
  flex: 1;
  min-height: 0;
  border-radius: 8px;
  display: flex;
  gap: 20px;
  padding: 0 20px;
}

.middle-left,
.middle-center,
.middle-right,
.bottom-left,
.bottom-center,
.bottom-right {
  border-radius: 4px;
  border: 1px solid rgba(24, 144, 255, 0.2);
  padding: 16px;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.middle-left,
.bottom-left {
  width: 25%;
  overflow-y: auto;
  /* 添加滚动条自定义样式 */
  scrollbar-width: thin;
  scrollbar-color: rgba(24, 144, 255, 0.3) rgba(0, 21, 41, 0.2);
}

.middle-center{
   padding: 0;
   border: none;
}

/* 自定义滚动条样式 - Webkit浏览器 */
.middle-left::-webkit-scrollbar {
  width: 6px;
}

.middle-left::-webkit-scrollbar-track {
  background: rgba(0, 21, 41, 0.2);
  border-radius: 3px;
}

.middle-left::-webkit-scrollbar-thumb {
  background: rgba(24, 144, 255, 0.3);
  border-radius: 3px;
  transition: background 0.3s;
}

.middle-left::-webkit-scrollbar-thumb:hover {
  background: rgba(24, 144, 255, 0.5);
}

/* 确保内容区域有合适的内边距，避免滚动条遮挡内容 */
.data-overview {
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  gap: 20px;
  padding: 12px;
  padding-right: 16px;
}

/* 调整内部元素间距，使其更紧凑 */
.total-elderly {
  margin-bottom: 20px;
}

.age-groups {
  margin-bottom: 20px;
}

.stats-grid {
  margin-bottom: 12px;
}

.middle-center,
.bottom-center {
  width: 50%;
}

.middle-right,
.bottom-right {
  width: 25%;
}

.panel-title {
  color: #fff;
  font-size: 1rem;
  font-weight: 500;
  margin: 0 0 16px 0;
  padding-left: 30px;
  text-align: left;
  flex-shrink: 0;
  height: 20px;
  line-height: 20px;
}

.data-overview {
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  gap: 10px;
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
  gap: 8px;
}

.stats-grid {
  display: flex;
  justify-content: center;
  align-content: center;
  align-items: center;
}

.stats-item {
  display: flex;
  justify-content: center;
  text-align: center;
}

.stats-label {
  color: rgba(255, 255, 255, 0.65);
  font-size: 0.8125rem;
}

.stats-value {
  color: #fff;
  font-size: 1rem;
  font-weight: 500;
  border: 1px solid rgba(24, 144, 255, 0.5);
}

.map-container,
.pie-chart,
.care-chart {
  width: 100%;
  flex: 1;
  min-height: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  background: transparent;
}

.care-chart {
  height: calc(100% - 36px) !important;
}

.total-value {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 20px;
}

.total-value .digit-box {
  color: #fff;
  font-size: 28px;
  font-weight: 500;
  width: 36px;
  height: 48px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 4px;
  background-image: url('src/assets/images/num_bg.png');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
}

.age-groups {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 50px;
}

.age-item {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.age-label {
  color: rgba(255, 255, 255, 0.85);
  font-size: 14px;
}

.age-value {
  display: flex;
  justify-content: center;
  align-items: center;
  color: #fff;
  font-size: 16px;
  font-weight: 500;
  gap: 10px;
}

.age-value .digit-box {
  color: #fff;
  font-size: 18px;
  font-weight: 500;
  width: 18px;
  height: 28px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 4px;
  background-image: url('src/assets/images/num_bg.png');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
}

.stats-grid {
  display: grid;
  grid-template-columns: 100px 100px;
  gap: 20px 100px;
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

/* 确保图表容器能够自适应 */
@media screen and (max-height: 900px) {
  .dashboard-header {
    height: 50px;
  }
  
  .dashboard-top {
    height: 50px;
  }
  
  .section-title {
    font-size: 1.5rem;
  }
  
  .panel-title {
    margin: 0 0 8px 0;
  }
  
  .data-overview {
    gap: 8px;
  }
}

@media screen and (max-height: 700px) {
  .dashboard-header {
    height: 40px;
  }
  
  .dashboard-top {
    height: 40px;
  }
  
  .dashboard-main {
    gap: 10px;
    padding: 10px;
  }
  
  .section-title {
    font-size: 1.25rem;
  }
}
</style>
