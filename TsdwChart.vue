<template>
  <div class="chart-container" ref="TsdwChart" />
</template>

<script>
import Echarts from 'echarts'
import { api } from './api'

export default {
  name: 'TsdwChart',
  props: {
    title: {
      type: String,
      required: true
    },
    path: {
      type: String,
      required: false,
      default () {
        return 'filestore://'
      }
    },
    queueName: {
      type: String,
      required: false,
      default () {
        return 'default_queue'
      }
    },
    selectType: {
      type: String,
      required: true
    },
    selectTable: {
      type: String,
      required: true
    },
    wfid: {
      type: String,
      required: false,
      default () {
        return '140604'
      }
    },
    wtid: {
      type: String,
      required: false,
      default () {
        return '140604006'
      }
    },
    startTime: {
      type: String,
      required: false,
      default () {
        return '2019-01-11 00:00:00.000'
      }
    },
    endTime: {
      type: String,
      required: false,
      default () {
        return '2019-01-11 23:59:59.000'
      }
    }
  },
  data () {
    return {
      tsdwData: null,
      seriesItem: {
        type: 'line',
        showSymbol: false,
        legendHoverLink: true,
        lineStyle: {
          width: 0.5
        }
      },
      option: {
        title: {
          text: this.title,
          textStyle: {
            color: '#666'
          },
          top: 10,
          left: 'center'
        },
        legend: {
          data: [],
          bottom: 0
        },
        toolbox: {
          feature: {
            dataZoom: {},
            restore: {},
            saveAsImage: {}
          }
        },
        tooltip: {},
        xAxis: {
          type: 'time',
          splitLine: {
            show: false
          }
        },
        yAxis: [
          {
            type: 'value',
            splitLine: {
              show: false
            },
            min: 'dataMin',
            max: 'dataMax'
          },
          {
            type: 'value',
            splitLine: {
              show: false
            },
            min: 'dataMin',
            max: 'dataMax'
          }
        ]
      }
    }
  },
  watch: {
    tsdwData: {
      handler (curVal, oldVal) {
        if (curVal) {
          this.showTsdwChart()
        }
      },
      deep: true
    }
  },
  created () {
    this.getTsdwData()
  },
  mounted () {
    this.init()
  },
  methods: {
    // 初始化图表
    init () {
      this.tsdwChart = Echarts.init(this.$refs.TsdwChart)
      this.tsdwChart.showLoading({ text: '加载中...' })
    },
    // 调用接口获取时序数据
    getTsdwData () {
      let queryParams = {
        // eslint-disable-next-line
        query: `select type, ${this.selectType} from ${this.selectTable} where ((type=\'${this.selectTable}\' and wfid = \'${this.wfid}\' and wtid = \'${this.wtid}\')) and ts >= \'${this.startTime}\' and ts <= \'${this.endTime}\'`,
        resultType: 'REST',
        path: this.path,
        queueName: this.queueName,
        timeout: 6000
      }

      this.$axios.post(`${api.timeSeries}`, queryParams).then(res => {
        this.tsdwData = res.data.body.results
      }).catch(() => {
        this.tsdwChart.hideLoading()
      })
    },
    // 时序折线图
    showTsdwChart () {
      let dataType = ['ts', 'type', 'wfid', 'wtid']
      let legendData = []
      // 获取图例
      Object.keys(this.tsdwData[0]).filter(item => {
        if (!dataType.includes(item)) {
          legendData.push(item)
        }
      })
      this.option.legend.data = legendData

      let option = {
        ...this.option,
        series: this.getSeriesData()
      }
      this.tsdwChart.hideLoading()
      this.tsdwChart.setOption(option)
      window.onresize = this.tsdwChart.resize
    },
    getSeriesData () {
      let legendData = this.option.legend.data
      let seriesList = []
      legendData.forEach((legendItem, index) => {
        seriesList.push({
          name: legendItem,
          yAxisIndex: index % 2 === 0 ? 0 : 1,
          ...this.seriesItem,
          markLine: {
            data: [{
              name: '平均值',
              type: 'average'
            }],
            symbol: 'circle',
            symbolSize: 2,
            label: {
              show: false
            }
          },
          data: this.getSeriesDataItem(legendItem)
        })
      })
      return seriesList
    },
    getSeriesDataItem (legendItem) {
      const seriesData = this.tsdwData.reduce((seriesList, item) => {
        seriesList.push([item.ts, item[legendItem]])
        return seriesList
      }, [])

      return seriesData
    }
  },
  beforDestory () {
    this.tsdwChart.clear()
  }
}
</script>

<style lang="css" scoped>
.chart-container {
  width: 100%;
  height: 100%;
  min-height: 360px;
}
</style>
