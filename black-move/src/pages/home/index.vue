<template>
  <div class="home">
    <div class="header">
      <img src="../../../static/logo.png" alt="">
      <p>OTC防骗联盟</p>
    </div>
    <div class="search">
      <div style="display: flex;background-color: #fff;align-items: center">
        <i class="el-icon-search" style="color: #999;font-size:.28rem;margin-left: .14rem"></i>
        <input type="text"
               :placeholder="placeholder"
               v-model="keyword"
               @keyup.enter="blacksSearch"
               @focus="focus" @blur="blur">
      </div>
      <p @click="blacksSearch" style="cursor:pointer">搜索</p>
    </div>
    <div class="banner" v-if="blacks.length">
      <img src="../../../static/num.png" alt="">
      <p>{{first?'曝光列表':'查询到'+blacks.length+'条'}}</p>
    </div>
    <div class="details" v-show="blacks.length>0 && show">
      <div v-for="(black, index) in blacks" class="oneself" v-if="index < check">
        <div class="item">
          <h3>风险评估：</h3>
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
        <div class="item" v-if="black.wx_no">
          <h3>微信号：{{black.wx_no}}</h3>
        </div>
        <div class="item" v-if="black.qq_no">
          <h3>QQ号：{{black.qq_no}}</h3>
        </div>
        <div class="item" v-if="black.mobile">
          <h3>手机号：{{black.mobile}}</h3>
        </div>
        <div class="item" v-if="black.name">
          <h3>姓名：{{black.name}}</h3>
        </div>
        <div class="item" v-if="black.id_no">
          <h3>证件号：{{black.id_no}}</h3>
        </div>
        <div class="item" v-if="black.symbol_name">
          <h3>被骗币种：{{black.symbol_name}}</h3>
        </div>
        <div class="item" v-if="black.wallet_address">
          <h3>骗子钱包地址：{{black.wallet_address}}</h3>
        </div>
        <div class="item" v-if="black.success_at_text">
          <h3>举报时间：{{black.success_at_text}}</h3>
        </div>
        <div class="item" v-if="black.images.length>0" style="align-items: flex-start;flex-direction: column;position: relative">
          <div>
            <div class="img_li"  v-if="index % 2 !=0 && index  < 6" v-for="(item,index) in black.images">
              <img :src="item" alt=""
                   @click="magnify(black.images,index)">
            </div>
          </div>
          <div class="pic-num" v-if="black.images.length>6">
            <p>{{black.images.length/2}}</p>
          </div>
        </div>
        <div class="item" v-if="black.black_content">
          <p>举报说明：{{black.black_content}}</p>
        </div>
      </div>
      <div class="more" @click="check += 10">
        <span>{{blacks.length > check ? '查看更多': '没有更多了'}}</span>
        <i class="el-icon-arrow-down"></i>
      </div>
    </div>
    <div v-if="showNull" class="please">
      <p>请输入搜索内容</p>
    </div>
    <div class="null" v-if="blacks.length===0 && show">
      <img src="../../../static/null.png" alt="">
      <span>未查询到信息～</span>
    </div>
    <div class="btn" @click="show_code = true">
      <img src="../../../static/btn.png" alt="">
    </div>
    <div class="shadow" v-if="shadow_img.length > 0" @click="clear">
      <wc-swiper :autoplay="false" :defaultSlide="Slide">
        <wc-slide v-for="(item, index) in shadow_img" :key="index">
          <img :src="item" alt="">
        </wc-slide>
      </wc-swiper>
    </div>
    <div class="shadow2" v-if="show_code">
      <div class="close">
        <img src="../../../static/close.png" alt="" @click="show_code = false">
      </div>
      <div class="code">
        <img src="../../../static/code.png" alt="">
        <p>扫描二维码，上报举证信息</p>
      </div>
    </div>
    <!--<img src="../../../static/banner.png" alt="" class="foot" @click="goTo(download_url)">-->
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
        shadow_img: [],
        showNull: false,
        placeholder: '可查询微信号/手机号/证件号/QQ号',
        flag: true,
        not: false,
        init: true,
        Slide: 0,
        check: 10,
        show_code: false,
        first: true,
        download_url: ''
      }
    },
    mounted () {
      this.blacksHot()
      this.download()
    },
    methods: {
      blacksSearch () {
        const that = this
        that.show = false
        that.first = false
        if (!that.keyword) {
          that.showNull = true
          that.blacks = []
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
      magnify (img, i) {
        this.shadow_img = img.filter((item, index) => index % 2 === 0)
        this.Slide = (i - 1) / 2
      },
      clear () {
        this.shadow_img = []
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
      },
      goTo (e) {
        window.location.href = e
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
  .home{
    width: 100%;
    height: 100%;
    margin:  0 auto;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;
    background-color: #F1F2F1;
    position: relative;
    display: -webkit-flex;
    -webkit-flex-direction: column;
    -webkit-justify-content: flex-start;
    -webkit-align-items:center;
  }
  .header{
    width: 100%;
    height: .84rem;
    display: flex;
    justify-content: center;
    align-items: center;
    display: -webkit-flex;
    -webkit-flex-direction: row;
    -webkit-justify-content: center;
    -webkit-align-items:center;
  }
  .header img{
    width: .3rem;
    height: .3rem;
  }
  .header p{
    font-size: .28rem;
    color: #666666;
    letter-spacing: 0;
    margin-left: .1rem;
  }
  .search{
    width: 100%;
    height: 1.24rem;
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    background-color: #F1F2F1;
    position: sticky;
    left: 0;
    top:0;
    z-index: 99;
    display: -webkit-flex;
    -webkit-flex-direction: row;
    -webkit-justify-content: center;
    -webkit-align-items:center;
  }
  .search input{
    width: 5.2rem;
    height: .72rem;
    font-size: .28rem;
    color: #999999;
    letter-spacing: 0;
    line-height: .72rem;
    padding-left: .1rem;
  }
  .search p{
    width: 1.16rem;
    height: .72rem;
    background: #005EB6;
    line-height: .72rem;
    text-align: center;
    font-size: .28rem;
    color: #FFFFFF;
  }
  .banner{
    display: flex;
    width: 100%;
    justify-content: flex-start;
    align-items: center;
    display: -webkit-flex;
    -webkit-flex-direction: row;
    -webkit-justify-content: flex-start;
    -webkit-align-items:center;
  }
  .banner img{
    width:.2rem;
    height:.22rem;
    margin-left: .4rem;
  }
  .banner p{
    font-size: .24rem;
    color: #605F65;
    letter-spacing: 0;
    margin-left: .1rem;
  }
  .details{
    height: 100%;
  }
  .oneself{
    display: flex;
    flex-direction: column;
    width: 6.9rem;
    background-color: #fff;
    margin-top: .2rem;
    padding: .2rem .3rem;
    display: -webkit-flex;
    -webkit-flex-direction: column;
  }

  .item{
    display: flex;
    justify-content: flex-start;
    align-items: center;
    margin-top: .2rem;
    display: -webkit-flex;
    -webkit-flex-direction: row;
    -webkit-justify-content: flex-start;
    -webkit-align-items:center;
  }
  .item h3{
    text-align: left;
    font-size: .3rem;
    color: #333;
    min-width: 1.6rem;
  }
  .item p{
    width: 100%;
    font-size: .28rem;
    color: #666;
    text-align: left;
  }
  .item > div{
    overflow: hidden;
    display: flex;
    flex-direction: row;
    align-items: center;
    flex-wrap:wrap;
    width: 100%;
    display: -webkit-flex;
    -webkit-flex-direction: row;
    -webkit-align-items:center;
  }
  .item .img_li{
    width: 2.2rem;
    height: 2.2rem;
    margin-left: .15rem;
    overflow: hidden;
    display: flex;
    align-items: center;
    justify-content: center;
    display: -webkit-flex;
    -webkit-flex-direction: row;
    -webkit-justify-content: center;
    -webkit-align-items:center;
  }
  .item .img_li:first-child{
    margin-left: 0;
  }
  .item div img{
    cursor:pointer;
    width: 100%;
    height: auto;
    object-fit: cover;
  }
  .more{
    background-color: #fff;
    margin-top: .2rem;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: .28rem;
    color: #555;
    letter-spacing: 0;
    height: .9rem;
    display: -webkit-flex;
    -webkit-flex-direction: row;
    -webkit-justify-content: center;
    -webkit-align-items:center;
  }
  .btn{
    position: fixed;
    right: .2rem;
    bottom: 1rem;
  }
  .btn img{
    width: 1.1rem;
    height: 1.1rem;
  }
  .shadow{
    background-color: rgba(0,0,0,.8);
    width: 100%;
    height: 100%;
    position: fixed;
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 999;
    display: -webkit-flex;
    -webkit-flex-direction: row;
    -webkit-justify-content: center;
    -webkit-align-items:center;
  }
  .shadow img{
    max-width: 6.9rem;
    max-height: 12rem;
    margin-bottom: 20px;
  }
  .shadow2{
    background-color: rgba(0,0,0,.8);
    width: 100%;
    height: 100%;
    position: fixed;
    left: 0;
    top: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    z-index: 999;
    display: -webkit-flex;
    -webkit-flex-direction: column;
    -webkit-justify-content: center;
    -webkit-align-items:center;
  }
  .shadow2 .close{
    width: 5rem;
    text-align: right;
    height: 1.1rem;
  }
  .shadow2 .close img{
    width: .44rem;
    height: .44rem;
  }
  .shadow2 .code{
    width: 5rem;
    height: 5.4rem;
    background-color: #fff;
    box-shadow: 0 2px 10px 0 rgba(0,0,0,0.29);
    border-radius: 10px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    display: -webkit-flex;
    -webkit-flex-direction: column;
    -webkit-justify-content: center;
    -webkit-align-items:center;
  }
  .shadow2 .code img{
    width: 4.16rem;
    height: 4.16rem;
  }
  .shadow2 .code p{
    font-size: .24rem;
    color: #333333;
    margin-top: .2rem;
  }
  .null{
    height: 100%;
    min-height: 600px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    display: -webkit-flex;
    -webkit-flex-direction: column;
    -webkit-justify-content: center;
    -webkit-align-items:center;
  }
  .null img{
    width:1.62rem;
    height: 1.30rem;
  }
  .null span{
    font-size: .3rem;
    color: #999999;
    margin-top: .4rem;
  }
  .please{
    width: 100%;height:100%;
    min-height: 600px;
  }
  .please p{
    font-size:.32rem;color: #000;
    margin-top: 200px;
  }
  .pic-num{
    position: absolute;
    right: 0;
    bottom: .1rem;
    display: flex;
    justify-content: flex-end;
    display: -webkit-flex;
    -webkit-flex-direction: row;
    -webkit-justify-content: flex-end;
  }
  .pic-num p{
    width: .4rem;
    height: .4rem;
    text-align: center;
    font-size: .3rem;
    color: #ddd;
    margin-right: .2rem;
    background: rgba(0,0,0,.6);
    border-radius: 5px;
  }
  .bg0{
    color: #000;
  }
  .bgf{
    color: #f2f2f2;
  }
  .foot{
    position: fixed;
    left: 0;
    bottom: 0;
    width: 100%;
    height: 1.10rem;
    border: 1px solid #000;
  }
</style>
