<template>
  <div id="app">
    <m-header v-show="!isShowCitylist"></m-header>
    <city ref="city" v-show="!isShowCitylist" :city="city" :cityList="cityList"></city>
    <citylist :cityList="cityList" :city="city" v-show="isShowCitylist"></citylist>
    <distribution v-show="!isShowCitylist" :distribution="distribution"></distribution>
  </div>
</template>

<script>
  import header from './components/header/header.vue';
  import city from './components/city/city.vue';
  import citylist from './components/citylist/citylist.vue';
  import distribution from './components/distribution/distribution.vue';
  export default {
    components: {
      'm-header': header,
      city,
      citylist,
      distribution
    },
    created() {
      // 请求支持的城市
      this.$http.get('http://apps.homed.me/payTest/juhe/server.php', {params: {'api': 'cityList'}}).then((res) => {
        let jsondata = JSON.parse(res.bodyText);
        this.cityList = jsondata.result;
      });
      // 根据城市请求污染源
      this.$http.get('http://apps.homed.me/payTest/juhe/server.php', {params: {'api': 'distribution', 'parm': '&city=' + this.city.code}}).then((res) => {
        let jsondata = JSON.parse(res.bodyText);
        let result = jsondata.result;
        // 节省请求次数
        result = result.slice(6, 7);
        this.distribution = result;
      });
    },
    data() {
        return {
          city: {},
          cityList: {},
          isShowCitylist: false,
          distribution: {}
        };
    },
    watch: {
      // 监听城市变化，根据城市请求污染源
      city: function () {
        this.$http.get('http://apps.homed.me/payTest/juhe/server.php', {params: {'api': 'distribution', 'parm': '&city=' + this.city.code}}).then((res) => {
          let jsondata = JSON.parse(res.bodyText);
          let result = jsondata.result;
          // 节省请求次数
          result = result.slice(6, 7);
          this.distribution = result;
        });
      }
    },
    methods: {
      showCityList() {
        this.isShowCitylist = !this.isShowCitylist;
      }
    }
  };
</script>

<style>
  #app {
    background-color: #f5f5f5;
    height: 100%;
  }
</style>
