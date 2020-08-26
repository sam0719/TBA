<template>
  <div id="lines" class="lines" style="width:100%;height: 500px">
    <v-chart id="echarts" ref="echarts" :options="lines" @zr:click="testclick" @zr:mousedown="rclick_back" @click="onClick" />
    <el-form class="demo-ruleForm">
      <el-form-item
        label="坐標"
        prop="axis"
      >
        <el-input ref="xaxis" v-model="numberValidateForm.xaxis" step="0.01" placeholder="x坐標" style="width: 100px;" type="axis" />
        <el-input ref="yaxis" v-model="numberValidateForm.yaxis" step="0.01" placeholder="y坐標" style="width: 100px;" type="axis" />
        <el-select v-model="value" placeholder="請選擇">
          <el-option
            v-for="item in select"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          />
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitForm">提交</el-button><el-button type="primary" @click="refresh">重置</el-button>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="dialogVisible = true">上傳文檔</el-button>
        當前路径长度:<countTo :start-val="start_score" :end-val="score" :duration="3000" :decimals="3" />
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
import 'echarts/lib/chart/effectScatter'
import 'echarts/map/js/china.js'
import '@/assets/map/taiwan.js'
import countTo from 'vue-count-to'

export default {
  components: {
    'v-chart': ECharts,
    countTo
  },
  data() {
    return {
      data_name: ['高雄市', '屏東縣', '臺南市', '新竹市', '新竹縣', '宜蘭縣', '基隆市', '苗栗縣', '臺北市', '新北市', '桃園縣', '彰化縣', '嘉義縣', '嘉義市', '花蓮縣', '南投縣', '臺中市', '雲林縣', '臺東縣', '澎湖縣', '金門縣', '連江縣'],
      data_coords: [[120.666, 23.01087], [120.62, 22.54951], [120.2513, 23.1417], [120.9647, 24.80395], [121.1252, 24.70328], [121.7195, 24.69295], [121.7081, 25.10898], [120.9417, 24.48927], [121.5598, 25.09108], [121.6739, 24.91571], [121.2168, 24.93759], [120.4818, 23.99297], [120.574, 23.45889], [120.4473, 23.47545], [121.3542, 23.7569], [120.9876, 23.83876], [120.9417, 24.23321], [120.3897, 23.75585], [120.9876, 22.98461], [119.6151, 23.56548], [118.3186, 24.43679], [119.5397, 26.19737]],
      select: [{
        value: '選項一',
        label: '空白'
      },
      {
        value: '選項二',
        label: '台灣地圖'
      },
      {
        value: '選項三',
        label: '世界地圖'
      }],
      value: '選項二',
      start_score: 0,
      score: 0,
      type: 'geo',
      finish: false,
      canclick: false, // 能否點擊
      data_tmp: { 'coords': [] },
      // 上傳文檔
      uploadFilename: null,
      uploadFiles: [],
      dialogVisible: false,
      numberValidateForm: {
        xaxis: '',
        yaxis: ''
      },
      series: [
        {
          name: '點',
          zlevel: 1,
          type: 'effectScatter',
          symbolSize: 5,
          tooltip: {
            show: true,
            formatter: function(param) {
              return '(' + param.data + ')'
            }
          },
          coordinateSystem: 'geo',
          data: []
        },
        {
          name: '線',
          type: 'lines',
          polyline: true,
          coordinateSystem: 'geo',
          effect: {
            show: true,
            period: 6,
            trailLength: 0,
            color: '#fff',
            symbol: 'path://M1705.06,1318.313v-89.254l-319.9-221.799l0.073-208.063c0.521-84.662-26.629-121.796-63.961-121.491c-37.332-0.305-64.482,36.829-63.961,121.491l0.073,208.063l-319.9,221.799v89.254l330.343-157.288l12.238,241.308l-134.449,92.931l0.531,42.034l175.125-42.917l175.125,42.917l0.531-42.034l-134.449-92.931l12.238-241.308L1705.06,1318.313z',
            symbolSize: 15
          },
          lineStyle: {
            normal: {
              width: 3,
              curveness: 0
            }
          },
          data: []
        },
        {
          type: 'map',
          map: 'taiwan',
          geoIndex: 0,
          z: 1,
          aspectScale: 0.75, // 地圖長寬比
          label: {
            normal: {
              show: false
            },
            emphasis: {
              show: false
            }
          },
          data: []
        }]
    }
  },
  computed: {
    data_tmp_coords() {
      return this.data_tmp.coords
    },
    lines: function() {
      return {
        backgroundColor: '#0F1C3C',
        title: {
          text: '空白'
        },
        legend: {
          textStyle: {
            color: '#fff'
          }
        },
        tooltip: {
          show: true,
          formatter: function(param) {
            return param.data.name
          }
        },
        toolbox: {
          left: 'right',
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
        geo: {
          map: 'taiwan',
          show: true,
          roam: true,
          label: {
            emphasis: {
              show: false
            }
          },
          layoutSize: '100%',
          itemStyle: {
            normal: {
              borderColor: 'auto',
              borderWidth: 3,
              shadowColor: 'rgba(10,76,139,1)',
              shadowOffsetY: 0,
              shadowBlur: 60
            }
          }
        },
        series: this.series
      }
    }
  },
  watch: {
    data_tmp_coords(val) {
      if (val.length >= 2) {
        var latitude = []
        var longitude = []
        this.start_score = this.score
        this.score = 0
        for (var i in val) {
          if (i !== '0') {
            if (this.type === 'geo') {
              latitude[0] = Math.PI * (parseInt(val[i][0]) + 5.0 * (val[i][0] - parseInt(val[i][0])) / 3.0) / 180.0
              latitude[1] = Math.PI * (parseInt(val[i - 1][0]) + 5.0 * (val[i - 1][0] - parseInt(val[i - 1][0])) / 3.0) / 180.0
              longitude[0] = Math.PI * (parseInt(val[i][1]) + 5.0 * (val[i][1] - parseInt(val[i][1])) / 3.0) / 180.0
              longitude[1] = Math.PI * (parseInt(val[i - 1][1]) + 5.0 * (val[i - 1][1] - parseInt(val[i - 1][1])) / 3.0) / 180.0
              var q1 = Math.cos(longitude[0] - longitude[1])
              var q2 = Math.cos(latitude[0] - latitude[1])
              var q3 = Math.cos(latitude[0] + latitude[1])
              this.score += parseInt(6378.388 * Math.acos(0.5 * ((1.0 + q1) * q2 - (1.0 - q1) * q3)) + 1.0)
            } else {
              this.score += Math.pow(Math.pow((val[i][0] - val[i - 1][0]), 2) + Math.pow((val[i][1] - val[i - 1][1]), 2), 0.5)
            }
          }
        }
      } else if (val.length === 0) {
        this.start_score = this.score
        this.score = 0
      }
      if (val[val.length - 1][0] === val[0][0] && val[val.length - 1][1] === val[0][1] && (val.length !== 1 && val.length !== 2)) {
        this.finished()
      }
    },
    value(val) {
      if (val === '選項一') {
        this.$router.push('Deployment')
      } else if (val === '選項三') {
        this.$router.push('Deployment_world')
      }
    }
  },
  mounted: function() {
    this.$nextTick(function() {
      for (var i in this.data_coords) {
        this.lines.series[0].data.push(this.data_coords[i])
      }
      for (var j in this.data_name) {
        this.lines.series[2].data.push({
          name: this.data_name[j],
          coords: this.data_coords[j]
        })
      }
    })
  },
  methods: {
    testclick(e) {
    },
    onClick(e) {
      if (e.componentSubType === 'effectScatter' || e.componentSubType === 'map') {
        var same = false
        var point = [arguments[0].value[0], arguments[0].value[1]]
        if (e.componentSubType === 'map') {
          point = [e.data.coords[0], e.data.coords[1]]
        }
        if (this.data_tmp.coords.length !== 0) {
          for (var i in this.data_tmp.coords) {
            if (i !== '0') {
              if (this.data_tmp.coords[i][0] === point[0] && this.data_tmp.coords[i][1] === point[1]) {
                same = true
                break
              }
            }
          }
        } else {
          this.data_tmp.coords.push(point)
          this.lines.series[1].data.push(this.data_tmp)
          same = true
        }
        if (!same) {
          this.data_tmp.coords.push(point)
        }
      }
    },
    submitForm() {
      var that = this
      that.lines.series[0].data.push([that.$refs.xaxis.value, that.$refs.yaxis.value])
    },
    refresh() {
      var seconds = 2
      const echarts = this.$refs.echarts
      echarts.showLoading({
        text: '加載中……',
        color: '#4ea397',
        maskColor: 'rgba(255, 255, 255, 0.4)'
      })
      const timer = setInterval(() => {
        seconds--
        if (seconds === 0) {
          clearInterval(timer)
          echarts.hideLoading()
          var echart = this.$refs.echarts
          echart.clear()
          if (this.type === 'geo') {
            this.data_tmp = { 'coords': [] }
            this.lines.series[1].data = []
          } else {
            this.data_tmp = { 'coords': [] }
            this.lines.series[1].data = []
            this.lines.series[0].data = []
          }
        }
      }, 1000)
    },
    rclick_back(e) {
      if (e.event.button === 2) { // 右鍵
        if (this.data_tmp.coords.length === 2) {
          this.data_tmp = { 'coords': [] }
          this.lines.series[1].data = []
        } else if (this.data_tmp.coords.length === 0) {
          console.log('down')
        } else {
          this.data_tmp.coords.pop()
        }
      }
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
          this.lines.series[0].data.push([tmp[j].substring(6, 11), tmp[j].substring(18, 23)])
        }
      }
    },
    finished() {
      this.$prompt('路徑長度為' + parseFloat(this.score).toFixed(3) + '<br/>請輸入姓名', '完成', {
        dangerouslyUseHTMLString: true,
        confirmButtonText: '確定',
        cancelButtonText: '取消'
      }).then(({ value }) => {
        this.$message({
          type: 'success',
          message: '已上傳,你的姓名是' + value
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '取消上傳'
        })
      })
    }
    // 右鍵菜單欄
  }
}
</script>

<style>
/**
 * 默认尺寸为 600px×400px，如果想让图表响应尺寸变化，可以像下面这样
 * 把尺寸设为百分比值（同时请记得为容器设置尺寸）。
 */
.echarts {
  width: 100%;
  height: 100%;
}
</style>
