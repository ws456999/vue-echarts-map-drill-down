<template>
  <div class="map">
    <div ref="map" style="height: 100%;width: 100%;"></div>
  </div>
</template>

<script>
import echarts from 'echarts'
import { cityMap, provinceMap, special } from 'maps/map-data'

let chinaMap = require('maps/china.json')
echarts.registerMap('china', chinaMap)

export default {
  data: () => ({
    dataList: [],
    myChart: {},
    mapdata: [],
    mapName: 'china',
    cityList: {}
  }),

  computed: {
    opt () {
      return {
        backgroundColor: '#404a59',
        title: {
          text: 'vue 地图 下钻',
          subtext: '要你好看',
          // sublink: 'http://www.pm25.in',
          left: 'center',
          textStyle: {
            color: '#fff'
          }
        },
        tooltip: {
          trigger: 'item'
        },
        legend: {
          orient: 'vertical',
          y: 'bottom',
          x: 'right',
          data: ['房源数量'],
          textStyle: {
            color: '#fff'
          }
        },
        geo: {
          type: 'map',
          map: this.mapName,
          zoom: 1,
          roam: true,
          label: {
            normal: {
              show: false,
              textStyle: {
                color: '#999',
                fontSize: 13
              }
            },
            emphasis: {
              show: true,
              textStyle: {
                color: '#fff',
                fontSize: 13
              }
            }
          },
          itemStyle: {
            normal: {
              areaColor: '#323c48',
              borderColor: '#111'
            },
            emphasis: {
              areaColor: '#2B91B7'
            }
          },
          data: this.mapdata
        }
      }
    }
  },

  methods: {
    async updateChart () {
      this.myChart.setOption(this.opt)
    },
    clickHandler (params) {
      let name = params.name

      if (name in provinceMap) {
        // 如果点击的是34个省、市、自治区，绘制选中地区的二级地图
        let _provinceMap = provinceMap
        let province = require(`maps/province/${_provinceMap[name]}`)

        province.features.forEach(v => {
          this.mapdata.push({name: v.properties.name})
        })
        echarts.registerMap(name, province)
        this.mapName = name
        this.updateChart()
      } else if (name in cityMap) {
        let _cityMap = cityMap
        // 如果是【直辖市/特别行政区】只有二级下钻
        if (special.indexOf(name) >= 0) {
          this.mapName = name
          this.updateChart()
        } else {
          // 显示县级地图
          let city = require(`maps/city/${_cityMap[name]}.json`)
          city.features.forEach(v => {
            this.mapdata.push({name: v.properties.name})
          })
          echarts.registerMap(name, city)
          this.mapName = name
          this.updateChart()
        }
      } else {
        this.mapName = 'china'
        this.updateChart()
      }
    },
    init () {
      // 初始化 echarts
      this.myChart = echarts.init(this.$refs.map)
      this.myChart.setOption(this.opt)
      this.myChart.on('click', this.clickHandler)
    }
  },

  mounted () {
    this.init()
  }
}
</script>

<style>
.map {
  height: 100%;
  width: 100%;
}

</style>
