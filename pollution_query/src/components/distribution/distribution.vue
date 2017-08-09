<template>
  <div class="distribution">
    <ul>
      <li v-for="item in distribution">
        <distributionitem :item="item"></distributionitem>
      </li>
    </ul>
  </div>
</template>

<script>
  import distributionitem from '../distribution-item/distribution-item.vue';
  export default {
    components: {
      distributionitem
    },
    props: {
      distribution: {}
    }
  };
</script>

<style lang="scss" rel="stylesheet/stylus">
  .distribution {
    padding: 0 0.27rem;
    background-color: #f5f5f5;
  }
</style>
