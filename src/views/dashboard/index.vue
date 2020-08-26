<template>
  <el-container>
    <el-main>
      <div class="app-container documentation-container">
        <el-button class="document-btn" target="_blank" type="primary"><router-link :to="'/Optimization/Numerical'">{{ $t('dashboard.Numerical') }}</router-link></el-button>
        <el-button class="document-btn" target="_blank" type="primary"><router-link :to="'/Optimization/Combinatorial'">{{ $t('dashboard.Combinatorial') }}</router-link></el-button>
        <el-button class="document-btn" target="_blank" type="primary"><router-link :to="'/Optimization/Ordinal'">{{ $t('dashboard.Ordinal') }}</router-link></el-button>
        <el-button class="document-btn" target="_blank" type="primary"><router-link :to="'/Optimization/Deployment'">{{ $t('dashboard.Deployment') }}</router-link></el-button>
        <el-button class="document-btn" target="_blank" type="primary"><router-link :to="'/ranking'">{{ $t('route.ranking') }}</router-link></el-button>
      </div>
    </el-main>
  </el-container>
</template>

<script>
import { mapGetters } from 'vuex'

export default {
  name: 'Dashboard',
  computed: {
    ...mapGetters([
      'name'
    ])
  },
  method: {
  }
}
</script>

<style lang="scss" scoped>
.dashboard {
  &-container {
    margin: 30px;
  }
  &-text {
    font-size: 30px;
    line-height: 46px;
  }
}
.documentation-container {
  margin: 50px;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-evenly;

  .document-btn {
    flex-shrink: 0;
    display: block;
    cursor: pointer;
    height: 60px;
    width: 200px;
    margin-bottom: 16px;
    line-height: 35px;
    font-size: 20px;
    text-align: center;
  }
}
</style>
