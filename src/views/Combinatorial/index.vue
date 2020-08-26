<template>
  <div class="app-container Optimization-container">    <el-main>
    <img :src="image">
  </el-main>
  </div>
</template>

<script>
import image from '@/assets/images/組合最佳化.png'

export default {
  name: 'Combinatorial',
  data() {
    return {
      image: image
    }
  }
}
</script>

<style lang="scss" scoped>
.Optimization-container{
  position: relative;
  width: 100%;
  height: calc(100vh - 84px);
}
img{
 width:auto;
 height:auto;
 max-width:100%;
 max-height:100%; }
</style>
