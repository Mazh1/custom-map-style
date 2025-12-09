<template>
  <div class="map-container">
    <div ref="mapContainer" class="map"></div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import 'ol/ol.css'
import Map from 'ol/Map'
import View from 'ol/View'
import TileLayer from 'ol/layer/Tile'
import XYZ from 'ol/source/XYZ'
import { fromLonLat } from 'ol/proj'

// 地图容器引用
const mapContainer = ref(null)

// 地图状态
const zoomLevel = ref(10)
const center = ref([0, 0])

// 地图实例
let map = null

// 初始化地图
const initMap = () => {
  try {
    // 创建本地瓦片图层
    // 注意: 如果瓦片不存在，地图将显示空白
    const tileLayer = new TileLayer({
      source: new XYZ({
        // 本地瓦片路径，基于public目录
        // 如果你的瓦片存放在 public/tiles 目录下
        // 路径格式应该是 /tiles/{z}/{x}/{y}.png
        url: '/tiles/{z}/{x}/{y}.png',
        // 设置最大缩放级别
        maxZoom: 13,
        // 允许跨域（本地开发需要）
        crossOrigin: 'anonymous',
        // 瓦片加载失败时的处理
        tileLoadFunction: (tile, src) => {
          tile.getImage().src = src
          // 监听加载错误
          tile.getImage().onerror = () => {
            console.log(`瓦片加载失败: ${src}`)
          }
        }
      }),
      // 设置透明度，方便观察
      opacity: 0.9
    })

    // 创建地图视图
    const view = new View({
      center: fromLonLat([116.4074, 39.9042]), // 北京坐标
      zoom: zoomLevel.value,
      minZoom: 8,
      maxZoom: 13,
      constrainResolution: true
    })

    // 创建地图
    map = new Map({
      target: mapContainer.value,
      layers: [tileLayer],
      view: view,
      // 禁用不必要的控件以简化界面
      controls: []
    })

  } catch (error) {
    console.error('地图初始化失败:', error)
  }
}

// 组件挂载时初始化地图
onMounted(() => {
  if (mapContainer.value) {
    initMap()
  }
})

// 组件卸载时清理地图
onUnmounted(() => {
  if (map) {
    map.setTarget(null)
    map = null
  }
})
</script>

<style scoped>
.map-container {
  display: flex;
  flex-direction: column;
  height: calc(100vh - 100px);
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

.map {
  flex: 1;
  width: 100%;
  min-height: 400px;
  border: 2px solid #ddd;
  border-radius: 8px;
  overflow: hidden;
  background-color: #091220;
}


/* 响应式设计 */
@media (max-width: 768px) {
  .map-info {
    flex-direction: column;
  }

  .map-container {
    height: auto;
    padding: 10px;
  }

  .map {
    height: 400px;
  }
}
</style>
