<template>
  <div>
    <div id="pie1"></div>
  </div>
</template>

<script>
import * as echarts from 'echarts'
import api from '../../http/api'
export default {
  data() {
    return {
      dataList: [],
      chart: null,
      dataArr: [],
      timerId: null
    }
  },
  created() {
    this.requestMax()
    this.timerId = setInterval(() => {
      this.dataArr = []
      this.dataList = []
      this.requestMax()
    }, 90000)
  },

  methods: {
    /* 初始化charts */
    initChart() {
      this.chart = echarts.init(document.getElementById('pie1'))
      let max = 0
      this.dataList.forEach((item) => (max += item.value))
      this.dataList.forEach((item) => {
        this.dataArr.push({
          value: item.value,
          name: item.name
        })
      })
      this.chart.setOption({
        color: ['#1994ff', '#f48483'],
        tooltip: {
          show: true,
          trigger: 'item'
        },
        legend: {
          width: '100%',
          textStyle: {
            color: '#ffffff',
            fontSize: 12
          },
          bottom: '5%',
          itemGap: 10,
          itemWidth: 5,
          itemHeight: 5,
          orient: 'horizontal',
          left: 'center',
          top: '0%'
        },
        series: [
          {
            name: '当日人数统计',
            type: 'pie',
            right: '0%',
            top: '15%',
            bottom: '5%',
            radius: ['50%', '80%'],
            label: {
              show: false,
              color: '#fff'
            },
            labelLine: {
              show: false
            },
            data: this.dataArr
          }
        ]
      })
    },
    requestMax() {
      this.$axios({
        methods: 'get',
        url: api.maxAndAll,
        params: {
          secret: '5f37767efca7a4c71c9aaab94be5f43d',
          time: '1680921792',
          access_token: this.$store.state.token
        }
      })
        .then((res) => {
          const { data } = res.data
          this.dataList = [
            {
              value: Object.values(data)[1],
              name: `最高在线数:${Object.values(data)[1]}`
            },
            {
              value: Object.values(data)[0],
              name: `当日登陆数:${Object.values(data)[0]} `
            }
          ]
          this.initChart()
        })
        .catch((err) => {
          console.log(err)
        })
    }
  },
  beforeDestroy() {
    clearInterval(this.timerId)
  }
}
</script>

<style scoped lang="scss">
#pie1 {
  height: calc(100vh / 4 - 100px);
  width: 230px;
}
</style>
