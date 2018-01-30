<template>
  <div>
    <div class="container">
      <h2>请提供您要举报的用户的信息</h2>
      <h3>*请尽可能的提供完整的资料信息，提高举报的准确率</h3>
      <div class="item">
        <p>微信号：</p>
        <el-input style="width: 850px;height: 40px;" v-model="wx_no"></el-input>
      </div>
      <div class="item">
        <p>QQ号：</p>
        <el-input style="width: 850px;height: 40px;" v-model="qq_no"></el-input>
      </div>
      <div class="item">
        <p>手机号：</p>
        <el-input style="width: 850px;height: 40px;" v-model="mobile"></el-input>
      </div>
      <div class="item">
        <p>姓名：</p>
        <el-input style="width: 850px;height: 40px;" v-model="name"></el-input>
      </div>
      <div class="item">
        <p>证件号：</p>
        <el-input style="width: 850px;height: 40px;" v-model="id_no"></el-input>
      </div>
      <div class="item">
        <p>银行卡号：</p>
        <el-input style="width: 850px;height: 40px;" v-model="bank_card_no"></el-input>
      </div>
      <div class="item">
        <p>被骗币种：</p>
        <el-select v-model="symbol_id" placeholder="请选择">
          <el-option
            v-for="item in symbols"
            :key="item.id"
            :label="item.name"
            :value="item.id">
          </el-option>
        </el-select>
      </div>
      <div class="item">
        <p>骗子钱包地址：</p>
        <el-input style="width: 850px;height: 40px;" v-model="wallet_address"></el-input>
      </div>
      <div class="item">
        <p>请输入举报内容：</p>
        <el-input
          type="textarea"
          style="width: 850px;height: 110px;"
          :rows="4"
          placeholder="请输入内容"
          v-model="black_content">
        </el-input>
      </div>
      <div class="item" style="align-items: flex-start">
        <p>证件信息：</p>
        <div :class="[list1.length === 0?'border':'']">
          <el-upload
            class="avatar-uploader"
            :action="baseUrl1 + '/images/upload'"
            name="image"
            :limit="3"
            :on-exceed="handleExceed1"
            :show-file-list="false"
            :on-success="handleAvatarSuccess1"
            :before-upload="beforeAvatarUpload">
            <div style="display: flex;flex-direction: row;flex-wrap:wrap">
              <img v-if="imageUrlList1.length > 0" :src="item" class="avatar" v-for="item in imageUrlList1">
            </div>
            <i v-if="imageUrlList1.length === 0" class="el-icon-plus avatar-uploader-icon"></i>
          </el-upload>
        </div>
      </div>
      <div class="item" style="align-items: flex-start">
        <p>举报截图：</p>
        <div :class="[list2.length === 0?'border':'']">
          <el-upload
            class="avatar-uploader"
            :action="baseUrl1 + '/images/upload'"
            name="image"
            :limit="10"
            :on-exceed="handleExceed2"
            :show-file-list="false"
            :on-success="handleAvatarSuccess2"
            :before-upload="beforeAvatarUpload">
            <div style="display: flex;flex-direction: row;flex-wrap:wrap">
              <img v-if="imageUrlList2.length > 0" :src="item" class="avatar" v-for="item in imageUrlList2">
            </div>
            <i v-if="imageUrlList2.length === 0" class="el-icon-plus avatar-uploader-icon"></i>
          </el-upload>
        </div>
      </div>
      <div class="item">
        <p></p>
        <div style="font-size:14px;color: #F75F40;">*最多可提交10张截图.单张图片不能大于 2M</div>
      </div>
      <div style="display: flex;justify-content: center">
        <p class="btn" @click="blacksCreate">提交</p>
      </div>
    </div>
  </div>
</template>

<script>
  import { baseUrl } from '../../utils/base'
  import { httpGet, httpPost} from '../../utils/index'
  export default {
    data () {
      return {
        symbols: [],
        wx_no: '',
        qq_no: '',
        mobile: '',
        name: '',
        id_no: '',
        bank_card_no: '',
        symbol_id: '',
        wallet_address: '',
        black_content: '',
        list1: [],
        list2: [],
        list10: [],
        list20: [],
        imageUrl1: '',
        imageUrlList1: [],
        imageUrl2: '',
        imageUrlList2: [],
        baseUrl1: ''
      }
    },
    mounted () {
      this.getSymbols()
      this.baseUrl1 = baseUrl
    },
    methods: {
      handleAvatarSuccess1 (res, file) {
        this.imageUrl1 = URL.createObjectURL(file.raw);
        this.imageUrlList1.push(this.imageUrl1);
        this.list1.push(file.response.image_url.big);
        this.list10.push(file.response.image_url.small);
      },
      handleAvatarSuccess2 (res, file) {
        this.imageUrl2 = URL.createObjectURL(file.raw);
        this.imageUrlList2.push(this.imageUrl2);
        this.list2.push(file.response.image_url.big);
        this.list20.push(file.response.image_url.small);
      },
      beforeAvatarUpload (file) {
        const isLt2M = file.size / 1024 / 1024 < 2;
        if (!isLt2M) {
          this.$message.error('上传头像图片大小不能超过 2MB!');
        }
        return isLt2M;
      },
      getSymbols () {
        const that = this
        httpGet('/symbols', {symbol_type:'fictitious_money'}, res => {
          if (res.data.error_code === 0) {
            that.symbols = res.data.symbols
          }
        })
      },
      initData () {
        this.wx_no = ''
        this.qq_no = ''
        this.mobile = ''
        this.name = ''
        this.id_no = ''
        this.bank_card_no = ''
        this.symbol_id = ''
        this.wallet_address = ''
        this.black_content = ''
        this.list1 = []
        this.list2 = []
        this.imageUrlList1 = []
        this.imageUrlList2 = []
      },
      blacksCreate () {
        if (!this.black_content) {
          this.$message.error('请输入举报内容');
          return
        }
        const that = this
        httpPost('/blacks/create', {
          wx_no: that.wx_no,
          qq_no: that.qq_no,
          mobile: that.mobile,
          name: that.name,
          id_no: that.id_no,
          bank_card_no: that.bank_card_no,
          symbol_id: that.symbol_id,
          wallet_address: that.wallet_address,
          black_content: that.black_content,
          front_image: that.list1[0],
          back_image: that.list1[1],
          hold_image: that.list1[2],
          small_front_image: that.list10[0],
          small_back_image: that.list10[1],
          small_hold_image: that.list10[2],
          image0: that.list2[0],
          image1: that.list2[1],
          image2: that.list2[2],
          image3: that.list2[3],
          image4: that.list2[4],
          image5: that.list2[5],
          image6: that.list2[6],
          image7: that.list2[7],
          image8: that.list2[8],
          image9: that.list2[9],
          small_image0: that.list20[0],
          small_image1: that.list20[1],
          small_image2: that.list20[2],
          small_image3: that.list20[3],
          small_image4: that.list20[4],
          small_image5: that.list20[5],
          small_image6: that.list20[6],
          small_image7: that.list20[7],
          small_image8: that.list20[8],
          small_image9: that.list20[9]
        }, function (res) {
          if (res.data.error_code === 0) {
            that.$message({
              message: '提交成功',
              type: 'success'
            })
            that.initData()
          } else {
            that.$message.error(res.data.error_reason);
          }
        })
      },
      handleExceed1 (files, fileList) {
        this.$message.warning(`当前限制选择 3 个文件`);
      },
      handleExceed2 (files, fileList) {
        this.$message.warning(`当前限制选择 10 个文件`);
      }
    }
  }
</script>

<style scoped>
  .container{
    margin: 0 auto;
    padding: 0 50px;
    background-color: #fff;
    height: 1300px;
    margin-top: 20px;
    padding-top: 39px;
    padding-bottom: 50px;
  }
  h2{
    font-size: 20px;
    color: #333333;
    letter-spacing: 0;
  }
  h3{
    font-size: 14px;
    color: #F75F40;
    letter-spacing: 0;
    margin-top: 27px;
    text-align: left;
  }
  .item{
    display: flex;
    justify-content: flex-start;
    align-items: center;
    margin-top: 24px;
    /*border: 1px solid red;*/
  }
  .item p{
    width: 140px;
    /*border: 1px solid red;*/
    padding-left: 50px;
    text-align: left;
    font-size: 14px;
    color: #333333;
    letter-spacing: 0;
  }
  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }
  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }
  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }
  .btn{
    background: #00488B;
    border-radius: 5px;
    width: 480px;
    height: 46px;
    line-height: 46px;
    text-align: center;
    font-size: 18px;
    color: #FFFFFF;
    letter-spacing: 0;
    margin-top: 29px;
  }
  .border{
    border: 1px dashed #d9d9d9;
  }
</style>
