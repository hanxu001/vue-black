<template>
  <div>
    <keep-alive>
      <router-view></router-view>
    </keep-alive>
    <footer-Bottom></footer-Bottom>
    <img src="../../../static/banner.png" alt="" class="foot" @click="goTo(download_url)">
  </div>
</template>

<script>
  import { httpGet } from '../../utils/index'
  export default {
    data () {
      return {
        download_url: ''
      }
    },
    mounted () {
      this.download()
    },
    methods: {
      goTo (e) {
        window.location.href = e
        httpGet('/blacks/click_report', {}, res => {
        })
      },
      download () {
        const that = this
        httpGet('/blacks/download', {}, res => {
          if (res.data.error_code === 0) {
            that.download_url = res.data.download_url
          }
        })
      }
    }
  }
</script>

<style scoped>
  .foot{
    position: fixed;
    left: 0;
    bottom: 0;
    width: 100%;
    height: 1.10rem;
    border: 1px solid #000;
    z-index: 9999;
  }
</style>
