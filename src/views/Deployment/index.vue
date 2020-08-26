<template>
  <div id="lines" class="lines" style="width:100%;height: 500px">
    <v-chart id="echarts" ref="echarts" :options="lines" @zr:contextmenu="contextmenu" @zr:mousedown.right="rclick_back" @click="onClick" />
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
          <el-upload :before-upload="beforeUpload" :file-list="uploadFiles" action="alert" :auto-upload="false" multiple :on-change="loadJsonFromFile">
            <el-button size="small" type="primary">Select a file</el-button>
            <div slot="tip">只能上傳text類型文檔</div>
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
    document.oncontextmenu = function() { return false }
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
      value: '選項一',
      start_score: 0,
      score: 0,
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
      // options設定
      lines: {
        title: {
          text: '空白'
        },
        legend: {},
        tooltip: {
          formatter: '({c})'
        },
        dataZoom: [
          {
            type: 'inside',
            disabled: false,
            xAxisIndex: 0,
            filterMode: 'empty'
          },
          {
            type: 'inside',
            disabled: false,
            yAxisIndex: 0,
            filterMode: 'empty'
          }
        ],
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
        xAxis: {
          max: 'dataMax',
          show: true,
          type: 'value',
          splitLine: {
            show: true
          }
        },
        yAxis: {
          min: 0,
          max: 'dataMax',
          show: true,
          type: 'value',
          splitLine: {
            show: true
          }
        },
        geo: {
          show: false,
          map: 'taiwan',
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
        series: [
          {
            name: '點',
            zlevel: 1,
            type: 'effectScatter',
            symbolSize: 5,
            coordinateSystem: 'cartesian2d',
            tooltip: {
              show: true,
              formatter: function(param) {
                return '(' + param.data + ')'
              }
            },
            data: [[1, 1], [12, 12]
            ]
          },
          {
            name: '線',
            type: 'lines',
            polyline: true,
            coordinateSystem: 'cartesian2d',
            effect: {
              show: false,
              period: 6,
              trailLength: 0.7,
              color: '#fff',
              symbolSize: 4
            },
            lineStyle: {
              normal: {
                width: 3,
                curveness: 0
              }
            },
            data: []
          }]
      }
    }
  },
  computed: {
    data_tmp_coords() {
      return this.data_tmp.coords
    }
  },
  watch: {
    data_tmp_coords(val) {
      if (val.length >= 2) {
        this.start_score = this.score
        this.score = 0
        for (var i in val) {
          if (i !== '0') {
            this.score += Math.pow(Math.pow((val[i][0] - val[i - 1][0]), 2) + Math.pow((val[i][1] - val[i - 1][1]), 2), 0.5)
          }
        }
      } else if (val.length === 0) {
        this.start_score = this.score
        this.score = 0
      }
      if (val[val.length - 1][0] === val[0][0] && val[val.length - 1][1] === val[0][1] && val.length !== 1) {
        this.finished()
      }
    },
    value(val) {
      if (val === '選項二') {
        this.$router.push('deployment_taiwan')
        // console.log(this.lines.geo.show)
        // this.lines.title.text = '台灣'
        // this.lines.legend = { textStyle: { color: '#fff' }}
        // this.lines.tooltip.formatter = function(param) { return param.data.name }
        // this.lines.dataZoom[0].disabled = true
        // this.lines.dataZoom[1].disabled = true
        // this.lines.xAxis.show = false
        // this.lines.yAxis.show = false // 關閉xy軸顯示
        // this.lines.geo.show = true // 打開地理地圖顯示
        // this.lines.backgroundColor = '#0F1C3C'
        // this.lines.series[0].coordinateSystem = 'geo'
        // this.lines.series[1].coordinateSystem = 'geo' // 更改點線座標系統為地理座標
        // console.log(this.lines.geo.show)
        // this.lines.series.push({
        //   type: 'map',
        //   map: 'taiwan',
        //   geoIndex: 0,
        //   z: 1,
        //   aspectScale: 0.75, // 地圖長寬比
        //   label: {
        //     normal: {
        //       show: false
        //     },
        //     emphasis: {
        //       show: false
        //     }
        //   },
        //   data: []
        // })
        // for (var j in this.data_name) {
        //   this.lines.series[2].data.push({
        //     name: this.data_name[j],
        //     coords: this.data_coords[j]
        //   })
        // }
      } else if (val === '選項三') {
        this.$router.push('Deployment_world')
      }
    }
  },
  mounted: function() {
    this.$nextTick(function() {})
  },
  methods: {
    onClick(e) {
      if (e.componentSubType === 'effectScatter') {
        var same = false
        var point = [arguments[0].value[0], arguments[0].value[1]]
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
          this.data_tmp = { 'coords': [] }
          this.lines.series[1].data = []
          this.lines.series[0].data = []
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
      if (this.uploadFiles) { // 如果有上傳文檔
        for (let i = 0; i < this.uploadFiles.length; i++) { // 上傳多個文檔時遍歷
          const file = this.uploadFiles[i]
          if (!file) continue
          const reader = new FileReader() // FileReader對象
          reader.onload = async(e) => {
            try {
              this.txt_process(e.target.result)
              const document = JSON.parse(e.target.result)
              console.log(document)
            } catch (err) {
              console.log(`上傳文檔錯誤, ${err.message}`)
              this.showSnackbar(`上傳文檔錯誤: ${err.message}`, 4000)
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
    beforeUpload() {
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
    },
    contextmenu() {
      console.log('right click')
    }
    // 右鍵菜單欄
    // showMenu: function(e, menus) {
    //   $('div', menubox).text(e.name)
    //   var menulistbox = $('ul', menubox).empty()
    //   $(menus).each(function(i, item) {
    //     var li = $("<li style='" + style_li + "'>" + item.name + '</li>')
    //       .mouseenter(function() {
    //         $(this).attr('style', style_li_hover)
    //       })
    //       .mouseleave(function() {
    //         $(this).attr('style', style_li)
    //       })
    //       .click(function() {
    //         item['fn'].call(this)
    //         menubox.hide()
    //       })
    //     menulistbox.append(li)
    //   })
    //   menubox.css({
    //     'left': event.x,
    //     'top': event.y
    //   }).show()
    // }
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
