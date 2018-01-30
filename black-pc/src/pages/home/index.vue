<template>
  <div class="home">
    <div class="search">
      <input type="text" :placeholder="placeholder" v-model="keyword"  @keyup.enter="blacksSearch" @focus="focus" @blur="blur">
      <p @click="blacksSearch" style="cursor:pointer">查询</p>
    </div>
    <div class="details" v-show="blacks.length>0 && show">
      <p class="details-p1" v-if="!init">
        本次搜索出<span style="color: #F75F40">{{blacks.length}}</span>条记录
      </p>
      <p class="details-p2" v-if="init">
        -------------------------------------------------------------曝光列表-------------------------------------------------------------
      </p>
      <div :class="[index?'border-top':'','oneself']" v-for="(black,index) in blacks" v-if="index < check">
        <div class="item">
          <h3>可疑度：</h3>
          <p>
            <i class="el-icon-star-on bg0" v-if="black.level > 0"></i>
            <i class="el-icon-star-on bg0" v-if="black.level > 1"></i>
            <i class="el-icon-star-on bg0" v-if="black.level > 2"></i>
            <i class="el-icon-star-on bg0" v-if="black.level > 3"></i>
            <i class="el-icon-star-on bg0" v-if="black.level > 4"></i>
            <i class="el-icon-star-on bgf" v-if="black.level < 5"></i>
            <i class="el-icon-star-on bgf" v-if="black.level < 4"></i>
            <i class="el-icon-star-on bgf" v-if="black.level < 3"></i>
            <i class="el-icon-star-on bgf" v-if="black.level < 2"></i>
            <i class="el-icon-star-on bgf" v-if="black.level < 1"></i>
          </p>
        </div>
        <div class="item" v-if="black.success_at_text">
          <h3>举报时间：</h3>
          <p>{{black.success_at_text}}</p>
        </div>
        <div class="item" v-if="black.wx_no">
          <h3>微信号：</h3>
          <p>{{black.wx_no}}</p>
        </div>
        <div class="item" v-if="black.qq_no">
          <h3>QQ号：</h3>
          <p>{{black.qq_no}}</p>
        </div>
        <div class="item" v-if="black.mobile">
          <h3>手机号：</h3>
          <p>{{black.mobile}}</p>
        </div>
        <div class="item" v-if="black.name">
          <h3>姓名：</h3>
          <p>{{black.name}}</p>
        </div>
        <div class="item" v-if="black.id_no">
          <h3>证件号：</h3>
          <p>{{black.id_no}}</p>
        </div>
        <div class="item" v-if="black.symbol_name">
          <h3>被骗币种：</h3>
          <p>{{black.symbol_name}}</p>
        </div>
        <div class="item" v-if="black.wallet_address">
          <h3>骗子钱包地址：</h3>
          <p>{{black.wallet_address}}</p>
        </div>
        <div class="item" v-if="black.black_content" style="align-items: flex-start">
          <h3>举报内容：</h3>
          <p style="width: 580px;">{{black.black_content}}</p>
        </div>
        <div class="item" v-if="black.images.length>0" style="align-items: flex-start">
          <h3>举报截图：</h3>
          <!--<div>-->
            <!--<img :src="item" alt=""-->
                 <!--v-if="index % 2 !=0"-->
                 <!--v-for="(item,index) in black.images"-->
                 <!--@click="magnify(black.images[index-1])"-->
            <!--&gt;-->
          <!--</div>-->
          <div>
            <div class="img_li"  v-if="index % 2 !=0" v-for="(item,index) in black.images">
              <img :src="item" alt=""
                   @click="magnify(black.images[index-1])"
              >
            </div>
          </div>
        </div>
      </div>
      <div class="more" @click="check += 10" v-if="blacks.length>10">
        <span>{{blacks.length > check ? '查看更多': '没有更多了'}}</span>
        <i class="el-icon-arrow-down"></i>
      </div>
    </div>
    <div class="please" v-if="showNull">
      <p>请输入搜索内容</p>
    </div>
    <div class="null container" v-if="blacks.length===0 && show">
      <img src="../../../static/null.png" alt="">
      <span>未查询到信息～</span>
    </div>
    <div class="shadow2" v-if="shadow_img" @click="clear">
      <img :src="shadow_img" alt="">
    </div>
    <div class="code">
      <img src="../../../static/code.png" alt="">
      <p>扫描二维码，上报举证信息</p>
    </div>
  </div>
</template>

<script>
  import { httpGet } from '../../utils/index'
  export default {
    data () {
      return {
        input: '',
        keyword: '',
        blacks: [],
        showBlack: false,
        show: false,
        shadow_img: '',
        showNull: false,
        placeholder: '请输入要查询的手机号/QQ号/微信号/身份证号/姓名',
        flag: true,
        not: false,
        init: true,
        check: 10
      }
    },
    mounted () {
      this.blacksHot()
    },
    methods: {
      blacksSearch () {
        const that = this
        if (!that.keyword) {
          that.showNull = true
          that.blacks = []
          that.show = false
          return
        }
        httpGet('/blacks/search', {keyword: that.keyword}, res => {
          that.show = true
          that.init = false
          that.showNull = false
          that.check = 10
          if (res.data.error_code === 0) {
            that.blacks = res.data.blacks
            that.keyword = ''
          }
        })
      },
      magnify (img) {
        this.shadow_img = img
      },
      clear () {
        this.shadow_img = ''
      },
      focus () {
        this.placeholder = ''
        this.not = true
      },
      blur () {
        this.placeholder = '请输入要查询的手机号/QQ号/微信号/身份证号/姓名'
      },
      blacksHot () {
        const that = this
        httpGet('/blacks/hot', {}, res => {
          that.show = true
          that.init = true
          that.showNull = false
          if (res.data.error_code === 0) {
            that.blacks = res.data.blacks
          }
        })
      }
    }
  }
</script>

<style scoped>
  .home{
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;
    padding-bottom: 50px;
  }
  .search{
    border-radius: 8px;
    display: flex;
    align-items: center;
    margin-top: 160px;
    margin-bottom: 50px;
  }
  .search input{
    width: 600px;
    height: 80px;
    color: #999999;
    font-size:24px;
    padding-left: 20px;
  }
  .search p{
    width: 160px;
    height: 88px;
    background: #00488B;
    line-height: 88px;
    text-align: center;
    font-size: 24px;
    color: #FFFFFF;
  }
  .details-p1{
    font-size:20px;color: #00488B;margin-top: 20px;text-align: right;width: 100%
  }
  .details-p2{
    font-size:20px;color: #00488B;margin-top: 20px;text-align: center;width: 100%
  }
  .oneself{
    display: flex;
    flex-direction: column;
    width: 910px;
    padding-bottom: 20px;
  }
  .item{
    display: flex;
    justify-content: flex-start;
    align-items: flex-start;
    padding-right: 38px;
    margin-top: 24px;
  }
  .item h3{
    margin-left: 50px;
    width: 160px;
    margin-right: 33px;
    text-align: left;
    font-size: 20px;
    color: #333;
  }
  .item p{
    font-size: 20px;
    color: #333;
    width: 200px;
    text-align: left;
  }
  .item div{
    width: 626px;
    height: auto;
    overflow: hidden;
    display: flex;
    justify-content: flex-start;
    align-items: flex-start;
    flex-wrap:wrap;
  }
  .please p{
    font-size:30px;color: #333;margin-top: 20px;
  }
  .null{
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    border-radius: 18px;
    overflow: hidden;
  }
  .null img{
    width:162px;
    height: 130px;
  }
  .null span{
    font-size: 18px;
    color: #333;
    margin-top: 60px;
  }
  .shadow2{
    background-color: rgba(0,0,0,.8);
    width: 100%;
    height: 100%;
    position: fixed;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    z-index: 9999;
  }
  .shadow2 img{
    max-width: 400px;
    max-height: 900px;
    margin-bottom: 20px;
  }
  .code{
    position: fixed;
    right: 0;
    bottom: 100px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    width: 260px;
  }
  .code img{
    width: 200px;
    height: 200px;
  }
  .code p{
    font-size: 16px;
    color: #000;
  }
  .more{
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 20px;
    color: #555;
    letter-spacing: 0;
    height: 40px;
  }
  .border-top{
    border-top: 1px solid #d9d9d9;
  }
  .bg0{
    color: #000;
  }
  .bgf{
    color: #f2f2f2;
  }
  .item .img_li{
    width: 30%;
    height: 300px;
    margin-left: 15px;
    overflow: hidden;
    display: flex;
    align-items: center;
    justify-content: center;
    display: -webkit-flex;
    -webkit-flex-direction: row;
    -webkit-justify-content: center;
    -webkit-align-items:center;
  }
  .item>.img_li:first-child{
    margin-left: 0;
  }
  .item .img_li img{
    cursor:pointer;
    width: 100%;
    height: auto;
    object-fit: cover;
  }
</style>


