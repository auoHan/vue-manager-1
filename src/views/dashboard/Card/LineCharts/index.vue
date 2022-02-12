<template>
  <!-- 容器 -->
  <div ref="charts" class="charts">1212</div>
</template>

<script>
import * as echarts from 'echarts'

export default {
  name: 'LineCharts',
  mounted() {
    const lineCharts = echarts.init(this.$refs.charts)
    lineCharts.setOption({
      xAxis: {
        // 隐藏x轴
        show: false,
        type: 'category'
      },
      yAxis: {
        // 隐藏y轴
        show: false
      },
      // 系列
      series: [
        {
          type: 'line',
          data: [23, 13, 28, 12, 34, 9, 29, 10, 44],
          smooth: true,
          // 拐点的样式的设置
          itemStyle: {
            opacity: 0
          },
          // 线条的样式
          lineStyle: {
            color: '#126bae'
          },
          labelLine: {

          },
          // 填充颜色设置
          areaStyle: {
            color: {
              type: 'linear',
              x: 0,
              y: 0,
              x2: 0,
              y2: 1,
              colorStops: [
                {
                  offset: 0,
                  color: '#126bae' // 0% 处的颜色
                },
                {
                  offset: 1,
                  color: '#fff' // 100% 处的颜色
                }
              ],
              global: false // 缺省为 false
            }
          }
        }
      ],
      // 布局调试
      grid: {
        left: 0,
        top: 0,
        right: 0,
        bottom: 0
      }
    })
  }
}
</script>

<style scoped>
.charts {
  height: 100%;
  width: 100%;
}
</style>
