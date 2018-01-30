<template>
  <div class="header">
    <div class="container">
      <div class="left" @click="$router.push('/')">
        <img src="../../../static/logo.png" alt="">
        <p>防骗联盟-<span>场外黑名单曝光系统</span></p>
      </div>
    </div>
  </div>
</template>

<style scoped>
  .header{
    width: 100%;
    min-width: 1000px;
    height: 60px;
    background-color: #5895d5;
    margin-bottom: 50px;
  }
  .container{
    width: 980px;
    margin:0 auto;
    display: flex;
    align-items: center;
    justify-content: space-between;
    /*border: 1px solid red;*/
  }
  .left{
    display: flex;
    align-items: center;
    justify-content: center;
    height: 60px;
    cursor:pointer;
  }
  .left img{
    width: 38px;
    height: 38px;
    margin-left: 20px;
    margin-right: 22px;
  }
  .left p{
    font-size: 24px;
    color: #fff;
    letter-spacing: 0;
    height: 60px;
    line-height: 60px;
  }
  .left p span{
    font-size: 18px;
    color: #fff;
    letter-spacing: 0;
  }
  .right{
    display: flex;
    justify-content: flex-end;
    align-items: center;
  }
  .item{
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 30px;
    height: 60px;
  }
  .item p{
    font-size: 16px;
    color: #000000;
    letter-spacing: 0;
  }
</style>
