<template>
  <div id="app">
    <h1>自定义地图效果</h1>
    <LocalTileMap />
  </div>
</template>

<script>
import LocalTileMap from './components/LocalTileMap.vue'
import { ref, onMounted } from 'vue'

export default {
  name: 'App',
  components: {
    LocalTileMap
  },
  setup() {
    const hasTiles = ref(false)

    onMounted(() => {
      // 检查是否有瓦片数据
      fetch('/tiles/0/0/0.png')
        .then(response => {
          if (response.ok) {
            hasTiles.value = true
          }
        })
        .catch(() => {
          hasTiles.value = false
        })
    })

    return {
      hasTiles
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  min-height: 100vh;
  background-color: #f5f5f5;
}

h1 {
  text-align: center;
  padding: 20px;
  background-color: white;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
</style>
