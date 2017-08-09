<template>
  <div class="citylist">
    <div class="currCity" @click="choseCity(city)">当前城市:{{city.region}}</div>
    <ul>
      <li v-for="item in cityList" class="citylist-city" @click="choseCity(item)">
        {{item.region}}
      </li>
    </ul>
  </div>
</template>

<script>
  export default {
    props: {
      city: {},
      cityList: {}
    },
    created() {
    },
    watch: {
      cityList: function() {
      }
    },
    methods: {
        choseCity(item) {
          this.$parent.city = item;
          this.$parent.showCityList();
        }
    }
  };
</script>

<style lang="scss">
  .citylist {
    padding: 0 0.27rem;
    font-size: 0.35rem;
    background-color: #f5f5f5;
    .currCity {
      height: 0.5rem;
      line-height: 0.5rem;
    }
    .citylist-city {
      height: 0.5rem;
      line-height: 0.5rem;
      border-bottom: 1px solid #111111;
    }
  }
</style>
