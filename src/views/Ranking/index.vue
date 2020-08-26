<template>
  <el-container>
    <el-header>
      <el-row>
        <el-col :offset="10"><h2>排行榜</h2></el-col>
      </el-row>
    </el-header>
    <el-main>
      <el-row>
        <el-col :span="12" :offset="6">
          <el-table
            :data="showTable"
            style="width: 100%"
          >
            <el-table-column
              prop="rank"
              label="排名"
              width="180"
            />
            <el-table-column
              prop="name"
              label="玩家"
              width="180"
            />
            <el-table-column
              prop="score"
              label="分數"
            />
          </el-table>
        </el-col>
        <el-col :span="24" style="text-align:center">
          <el-pagination
            layout="total,prev, pager, next"
            :page-size="pagesize"
            :total="tableData.length"
            @current-change="handleCurrentChange"
          />
        </el-col>
      </el-row>
    </el-main>
  </el-container>
</template>

<script>

export default {
  name: 'Deployment',
  data() {
    return {
      pagesize: 5,
      currentPage: 1,
      tableData: [{
        rank: '1',
        name: '薛丁格',
        score: 1314
      },
      {
        rank: '2',
        name: '拉拉熊',
        score: 824
      },
      {
        rank: '3',
        name: '麻吉兔',
        score: 520
      },
      {
        rank: '4',
        name: '史努比',
        score: 100
      },
      {
        rank: '5',
        name: '熊大',
        score: 23
      },
      {
        rank: '6',
        name: '米奇',
        score: 2
      }
      ]
    }
  },
  computed: {
    showTable() {
      return this.tableData.slice(
        (this.currentPage - 1) * this.pagesize,
        this.currentPage * this.pagesize
      )
    }
  },
  methods: {
    handleCurrentChange(currentPage) {
      this.currentPage = currentPage
    }
  }

}
</script>

<style lang="scss" scoped>
.Optimization-container{
  position: relative;
  width: 100%;
  height: 90%;
}
img{
 width:auto;
 height:auto;
 max-width:100%;
 max-height:100%; }
</style>
