<template>
  <div class="footer">
    <div class="moon">
      <img src="../../../static/logo.png" alt="">
      <p>OTC防骗联盟</p>
    </div>
    <p class="night">最专业的币圈黑名单曝光系统</p>
  </div>
</template>

<script>
  export default {
    data () {
      return {
      }
    },
    computed: {
//      ...mapState([''])
    },
    mounted () {
    },
    methods: {
//      ...mapMutations([''])
    }
  }
</script>

<style scoped>
  .footer{
    width: 100%;
    height: 100px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    background: #F1F2F1;
    margin-bottom: 1.1rem;
  }
  .moon{
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .moon img{
    width: .3rem;
    height: .3rem;
  }
  .moon p{
    font-size: .28rem;
    color: #666666;
    letter-spacing: 0;
    margin-left: .1rem;
  }
  .night{
    font-size: .24rem;
    color: #999999;
    letter-spacing: 0;
    margin-top: .15rem;
  }
</style>
