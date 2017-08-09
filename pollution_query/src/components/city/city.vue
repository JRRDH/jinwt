<template>
  <div class="city">
    <!--allmap->地图容器，无内容-->
    <div id="allmap"></div>
    <div class="cityName">
      <span class="location" @click="baiduLocation()">も</span>
      <span class="cityName-text"  @click="callShowCityList">{{city.region}}</span>
    </div>
    <div class="time">{{refreshTime}}</div>
  </div>
</template>

<script>
  export default {
    props: {
      city: {},
      cityList: {}
    },
      data() {
          return {
            time: '更新时间 2017-7-7'
          };
      },
    created() {
    },
    watch: {
      cityList: function() {
      }
    },
    mounted: function () {
      // 组件加载后触发定位
      this.$nextTick(function () {
        this.baiduLocation();
      });
    },
    methods: {
      callShowCityList() {
          this.$parent.showCityList();
      },
      baiduLocation() {
        let _that = this;
        /*eslint-disable*/
        // 百度地图定位
        var map = new BMap.Map("allmap");
        var point = new BMap.Point(116.331398, 39.897445);
        map.centerAndZoom(point, 12);
        var geoc = new BMap.Geocoder();
        var geolocation = new BMap.Geolocation();
        geolocation.getCurrentPosition(function(r){
          if(this.getStatus() === BMAP_STATUS_SUCCESS) {
            let mk = new BMap.Marker(r.point);
            map.addOverlay(mk);
            map.panTo(r.point);
            console.log('定位成功');
            geoc.getLocation(r.point, function(rs){
              var addComp = rs.addressComponents;
              for (let index in _that.cityList) {
                if ( addComp.city === _that.cityList[index].region) {
                  _that.$parent.city = _that.cityList[index];
                }
              }
            });
          }
          else {
            console.log('failed' + this.getStatus());
          }
        }, {enableHighAccuracy: true});
        /*eslint-disable*/
      }
    },
    computed: {
      // 接口返回的数据全是按小时刷新的，所以这里没有做判断，默认全写按整点刷新了
      refreshTime() {
          let curDate = new Date();
          return '更新时间: ' + curDate.getFullYear() + '/' + (curDate.getMonth() + 1) + '/' + curDate.getDate() + '  ' + curDate.getHours() + ':00';
      }
    }
  };
</script>

<style lang="scss">
  .city{
    margin: 0.18rem 0.27rem;
    font-family: PingFang-SC-Medium;
    .cityName{
      height: 0.47rem;
      line-height: 0.47rem;
      font-size: 0.35rem;
      .location{
        font-family: iconfont;
      }
      .cityName-text{
        color: #111111;
      }
    }
    .time {
      font-size: 0.22rem;
      line-height: 0.35rem;
      color: #999999;
    }
  }
</style>
