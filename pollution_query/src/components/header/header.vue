<template>
  <div class="header" :class="{blank: needBlank}">
    <div class="back" @click="goBack()"><span class="back-icon">亃</span>返回</div>
    <div class="title">城市污染查询</div>
  </div>
</template>

<script>
  export default {
    methods: {
      goBack() {
        window.history.go(-1);
      }
    },
    computed: {
      needBlank() {
        let isApp = !!(navigator.userAgent.search(/micromessenger/i) < 0);
        let isIos = !!navigator.userAgent.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/);
        return isApp && isIos;
      }
    }
  };
</script>

<style lang="scss">
  .blank {
    padding-top: 0.36rem;
  }
  .header {
    display: flex;
    border-bottom: solid 1px #EEEEEE;
    background-color: #FFFFff;
    height: 0.8rem;
    line-height: 0.8rem;
    font-size: 0.32rem;
    font-family: PingFang-SC-Bold;
    color: #4c4c4c;
    .back{
      position: absolute;
      padding-left: 0.27rem;
      flex: 1;
      width: 1rem;
      color: #f28300;
      .back-icon {
        font-family: iconfont;
      }
    }
    .title{
      flex: 1;
      text-align: center;
    }
  }
</style>
