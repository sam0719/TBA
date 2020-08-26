<template>
  <div class="e">
    <v-chart id="echarts" ref="echarts" :options="polar" @ready="onReady" @click="onClick" />
    <el-form class="demo-ruleForm">
      <el-form-item
        label="坐標"
        prop="axis"
        :rules="[
          { required: true, message: '坐標不能为空'},
          {
            validator(rule, value, callback) {
              var reg = /^-?\d{1,5}(?:\.\d{1,3})?$/
              if (reg.test(value)) {
                callback()
              } else {
                callback(new Error('請輸入不超過3位小數的數字'))
              }
            },
            trigger: 'blur'
          }
        ]"
      >
        <el-input ref="xaxis" v-model="numberValidateForm.xaxis" step="0.01" placeholder="x坐標" style="width: 100px;" type="axis" />
        <el-input ref="yaxis" v-model="numberValidateForm.yaxis" step="0.01" placeholder="y坐標" style="width: 100px;" type="axis" />
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitForm">提交</el-button><el-button type="primary" @click="refresh">重置</el-button>
        <el-button v-if="!isdraw" type="primary" @click="draw">連線</el-button>
        <el-button v-if="isdraw" type="primary" @click="ok">確定</el-button><el-button v-if="isdraw" type="primary" @click="cancel">取消</el-button>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="dialogVisible = true">上傳文檔</el-button>
        <el-button type="primary" @click="test">測試</el-button>
        <el-dialog title="Load JSON document from file" :visible.sync="dialogVisible">
          <el-upload :file-list="uploadFiles" action="alert" :auto-upload="false" multiple :on-change="loadJsonFromFile">
            <el-button size="small" type="primary">Select a file</el-button>
            <div slot="tip">upload only jpg/png files, and less than 500kb</div>
          </el-upload>
          <span slot="footer">
            <el-button type="primary" @click="dialogVisible = false">cancel</el-button>
            <el-button type="primary" @click="loadJsonFromFileConfirmed">confirm</el-button>
          </span>
        </el-dialog>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import ECharts from 'vue-echarts'

export default {
  components: {
    'v-chart': ECharts
  },
  data() {
    return {
      isdraw: false, // 能否連線
      canclick: false, // 能否點擊
      data: [],
      data_line: [],
      data_tmp: [],
      count: 0,
      numberValidateForm: {
        xaxis: '',
        yaxis: ''
      },
      // 上傳文檔
      uploadFilename: null,
      uploadFiles: [],
      dialogVisible: false
      // polar: {
      //   title: {
      //     text: '极坐标双数值轴'
      //   },
      //   legend: {
      //     data: ['line']
      //   },
      //   polar: {
      //     center: ['50%', '54%']
      //   },
      //   tooltip: {
      //     trigger: 'axis',
      //     axisPointer: {
      //       type: 'cross'
      //     }
      //   },
      //   angleAxis: {
      //     type: 'value',
      //     startAngle: 0
      //   },
      //   radiusAxis: {
      //     min: 0
      //   },
      //   series: [
      //     {
      //       coordinateSystem: 'polar',
      //       name: 'line',
      //       type: 'line',
      //       showSymbol: false,
      //       data: data
      //     }
      //   ],
      //   animationDuration: 4000
      // }
    }
  },
  computed: {
    polar: function() {
      return {
        legend: {},
        title: {
          text: '坐標軸'
        },
        tooltip: {
          formatter: '({c})'
        },
        toolbox: {
          left: 'center',
          feature: {
            dataZoom: { title: {
              zoom: '區域縮放',
              back: '區域縮放還原'
            }},
            dataView: { title: '數據視圖' },
            restore: { title: '還原' },
            saveAsImage: { title: '保存為圖片' }
          }
        },
        xAxis: {
          type: 'value',
          min: 'dataMin',
          max: 'dataMax',
          splitLine: {
            show: true
          }
        },
        yAxis: {
          type: 'value',
          min: 'dataMin',
          max: 'dataMax',
          splitLine: {
            show: true
          }
        },
        dataZoom: [
          {
            type: 'slider',
            show: false,
            xAxisIndex: [0],
            filterMode: 'none',
            rangeMode: ['percent', 'percent']
          },
          {
            type: 'slider',
            show: false,
            yAxisIndex: [0],
            filterMode: 'none',
            rangeMode: ['percent', 'percent']
          },
          {
            type: 'inside',
            xAxisIndex: [0],
            filterMode: 'none',
            rangeMode: ['percent', 'percent']
          },
          {
            type: 'inside',
            yAxisIndex: [0],
            filterMode: 'none',
            rangeMode: ['percent', 'percent']
          }
        ],
        series: [{
          symbolSize: 12,
          data: this.data,
          type: 'scatter'
        },
        {
          data: this.data_line,
          polyline: true,
          type: 'lines'
        },
        {
          data: this.data_tmp,
          type: 'line'
        }],
        animationDuration: 1000,
        animationEasing: 'linear',
        animationDurationUpdate: 3000
      }
    }
  },
  methods: {
    test() {
      const echart = this.$refs.echarts
      echart.clear()
      this.polar.animationDuration = 5000
      for (var i = -5; i <= 5; i = i + 0.1) {
        var x = 100 * Math.pow((Math.sin(i)), 3)
        var y = 13 * Math.cos(i) - 5 * Math.cos(2 * i) - 2 * Math.cos(3 * i) - Math.cos(4 * i)
        this.polar.series[1].data.push([x, y])
      }
    }, // 測試用
    onReady(instance) {
      const that = this
      console.log('ready')
      that.chart = instance
      that.data.push([1, 2], [3, 4])
    },
    submitForm: function() {
      var that = this
      that.data.push([that.$refs.xaxis.value, that.$refs.yaxis.value])
    },
    onClick(event, instance, echarts) {
      var that = this
      // const echart = that.$refs.echarts
      if (this.canclick) {
        // echart.clear()
        // that.data_tmp.push([arguments[0].value[0], arguments[0].value[1]])
        that.polar.series[1].data.coords.push([arguments[0].value[0], arguments[0].value[1]])
      }
    },
    draw() {
      this.isdraw = true
      this.canclick = true
    },
    ok() {
      const echart = this.$refs.echarts
      echart.clear()
      this.data_line = this.data_line.concat(this.data_tmp)
      this.data_tmp = []
      this.canclick = false
      this.isdraw = false
    },
    cancel() {
      this.data_tmp = []
      this.isdraw = false
      this.canclick = false
    },
    refresh() {
      this.seconds = 1
      const echarts = this.$refs.echarts
      echarts.showLoading({
        text: '加載中……',
        color: '#4ea397',
        maskColor: 'rgba(255, 255, 255, 0.4)'
      })
      const timer = setInterval(() => {
        this.seconds--
        console.log(this.seconds)
        if (this.seconds === 0) {
          clearTimeout(timer)
          echarts.hideLoading()
          var echart = this.$refs.echarts
          echart.clear()
          this.data = []
          this.data_line = []
          this.data_tmp = []
        }
      }, 1000)
    },
    // 上傳文檔
    loadJsonFromFile(file, fileList) {
      this.uploadFilename = file.name
      this.uploadFiles = fileList
    },
    loadJsonFromFileConfirmed() {
      console.log(this.uploadFiles)
      if (this.uploadFiles) {
        for (let i = 0; i < this.uploadFiles.length; i++) {
          const file = this.uploadFiles[i]
          console.log(file.raw)
          if (!file) continue
          const reader = new FileReader()
          reader.onload = async(e) => {
            try {
              this.txt_process(e.target.result)
              const document = JSON.parse(e.target.result)
              console.log(document)
            } catch (err) {
              console.log(`load JSON document from file error: ${err.message}`)
              this.showSnackbar(`Load JSON document from file error: ${err.message}`, 4000)
            }
          }
          reader.readAsText(file.raw)
        }
      }
      this.dialogVisible = false
    },
    txt_process(txt) {
      var tmp = []
      console.log('txt:' + txt)
      tmp = txt.trim().split('\n')
      for (var i = 0; i < tmp.length; i++) {
        // eslint-disable-next-line eqeqeq
        if (tmp[i] == 'NODE_COORD_SECTION') {
          break
        }
      }
      for (var j = i + 1; j < tmp.length; j++) {
        // eslint-disable-next-line eqeqeq
        if (tmp[j] == 'EOF') {
          break
        } else {
          this.polar.series[0].data.push([tmp[j].substring(6, 11), tmp[j].substring(18, 23)])
        }
      }
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
.e {
  width: 700px;
  height: 400px;
}
.echarts {
  width: 100%;
  height: 100%;
}
</style>
