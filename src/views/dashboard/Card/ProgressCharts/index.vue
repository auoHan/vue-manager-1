<template>
  <div ref="charts" class="charts" />
</template>

<script>
import * as echarts from 'echarts'

export default {
  name: 'BarCharts',
  mounted() {
    const progressCharts = echarts.init(this.$refs.charts)
    progressCharts.setOption({
      xAxis: {
        // 隐藏x轴
        show: false,
        // 最小值与最大值的设置
        min: 0,
        max: 100
      },
      yAxis: {
        // 隐藏y轴
        show: false,
        // 均分
        type: 'category'
      },
      // 系列
      series: [
        {
          // 图标形式-柱状图
          type: 'bar',
          data: [88],
          // 柱状图的宽度
          barWidth: 10,
          color: '#ff9900',
          // 背景颜色设置
          showBackground: true,
          // 设置背景颜色
          backgroundStyle: {
            color: '#eee'
          },
          // 文本标签
          label: {
            show: true,
            // 改变文本内容
            formatter: '|',
            // 文本标签位置调试
            position: 'right'
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
