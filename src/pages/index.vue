<template>
<div>
  <div class="container">
    <a class="link"
      v-for="(chart, index) of charts"
      v-if="chart.path !== '/pages/index'"
      :key="index"
      :href="chart.path">{{chart.config.navigationBarTitleText}}</a>
  </div>
  <ad unit-id="adunit-162e15806841e6e8"></ad>
</div>
</template>

<script>
import charts from '@/pages'

export default {
  data () {
    return {
      charts
    }
  }
}
</script>

<style scoped>
.link {
  width: 50%;
  text-align: center;
  padding: 10px 0;
}
</style>
