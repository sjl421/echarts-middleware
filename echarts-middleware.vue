<template>
  <div v-once class="echarts"></div>
</template>

<script>
  import echarts from 'echarts/lib/echarts'
  // echarts实例
  export default {
    props: {
      value: null,
      opt: Object,
      size: Object,
      theme: [String, Object],
      renderer: {
        type: String,
        default: 'canvas'
      }
    },
    data () {
      return {
        chart: null
      }
    },
    mounted () {
      const opt = this.opt
      let width, height
      if (!echarts) {
        console.error('本组件需要配合echarts组件使用,请运行npm i -save echarts安装!')
        return null
      }
      // 判断用户是否指定了组件的 宽 和 高，如果指定了，那么使用用户指定的值
      // 如果没有指定则使用 父组件 宽高
      // 如果出错 使用400px默认宽高
      if (this.size && this.size.w && this.size.h) {
        width = this.size.w
        height = this.size.h
      }
      if (!width && !height) {
        const parent = this.$el.parentNode
        if (parent && parent.clientWidth && parent.clientHeight) {
          height = parent.clientHeight
          width = parent.clientWidth
        } else {
          height = width = 400
          console.error('图表 宽度(w) 和 高度(h) 未设置!')
        }
      }
      // 注册echarts
      if (opt) {
        let chart = null
        setTimeout(() => {
          const renderer = this.renderer
          chart = echarts.init(this.$el, this.theme, {width, height, renderer})
          // 绘制图表
          chart.setOption(opt)
          this.chart = chart
          // 判断是否绑定v-model，如果绑定了就将值传给v-model
          if (this.value !== undefined) {
            this.$emit('input', chart)
          }
        }, 0)
      }
    },
    watch: {
      size: {
        handler: function (val, oldVal) {
          // 如果高度配置发生改变，更改图表大小
          this.chart.resize({
            width: val.w,
            height: val.h
          })
        },
        deep: true
      }
    },
    beforeDestroy () {
      if (this.chart) {
        this.chart.dispose()
        this.chart = null
      }
    }
  }
</script>
