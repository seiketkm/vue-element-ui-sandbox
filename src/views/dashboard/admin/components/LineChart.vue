<template>
  <div :class="className" :style="{ height: height, width: width }" />
</template>

<script>
import echarts from 'echarts'
require('echarts/theme/macarons') // echarts theme
import resize from './mixins/resize'

export default {
  mixins: [resize],
  props: {
    className: {
      type: String,
      default: 'chart'
    },
    width: {
      type: String,
      default: '100%'
    },
    height: {
      type: String,
      default: '350px'
    },
    autoResize: {
      type: Boolean,
      default: true
    },
    chartData: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      chart: null
    }
  },
  watch: {
    chartData: {
      deep: true,
      handler(contextElement) {
        this.setOptions(contextElement)
      }
    }
  },
  mounted() {
    this.$nextTick(() => {
      this.initChart()
    })
  },
  beforeDestroy() {
    if (!this.chart) {
      return
    }
    this.chart.dispose()
    this.chart = null
  },
  methods: {
    initChart() {
      this.chart = echarts.init(this.$el, 'macarons')
      this.setOptions(this.chartData)
    },
    setOptions(contextElement) {
      const chartdata = contextElement.attributes.map(item => {
        return {
          name: item.name,
          values: item.values.map(e => parseFloat(e.attrValue)),
          recvTime: item.values.map(e => new Date(e.recvTime))
        }
      })

      this.chart.setOption({
        xAxis: chartdata.map(data => {
          return {
            data: [...data.recvTime],
            boundaryGap: false,
            axisTick: {
              show: false
            }
          }
        }),
        grid: {
          left: 10,
          right: 10,
          bottom: 20,
          top: 30,
          containLabel: true
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross'
          },
          padding: [5, 10]
        },
        yAxis: {
          axisTick: {
            show: false
          }
        },
        legend: {
          data: [chartdata.map(data => data.name)]
        },
        series: chartdata.map(data => {
          return {
            name: data.name,
            itemStyle: {
              normal: {
                color: '#FF005A',
                lineStyle: {
                  color: '#FF005A',
                  width: 2
                }
              }
            },
            smooth: true,
            type: 'line',
            data: data.values,
            animationDuration: 2800,
            animationEasing: 'cubicInOut'
          }
        })
      })
    }
  }
}
</script>
