<template>
  <el-container>
    <el-main>
      <img :src="image">
    </el-main>
  </el-container>
</template>

<script>
import image from '@/assets/images/排列最佳化遊戲模式.png'

export default {
  name: 'Ordinal',
  data() {
    return {
      image: image
    }
  }
}
</script>
