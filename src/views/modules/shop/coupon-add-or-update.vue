<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" label-width="120px">
      <el-form-item prop="name" :label="$t('coupon.couponName')">
        <el-input v-model="dataForm.name" :placeholder="$t('coupon.couponName')"></el-input>
      </el-form-item>
      <el-form-item prop="pic" :label="$t('coupon.couponPic')">
        <el-input v-model="dataForm.pic" :placeholder="$t('coupon.couponPic')"></el-input>
      </el-form-item>
      <el-form-item prop="platform" :label="$t('coupon.couponPlatform')">
        <el-radio-group v-model="dataForm.platform">
          <el-radio :label="0">{{ $t('coupon.couponPlatform0') }}</el-radio>
          <el-radio :label="1">{{ $t('coupon.couponPlatform1') }}</el-radio>
          <el-radio :label="2">{{ $t('coupon.couponPlatform2') }}</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item prop="type" :label="$t('coupon.couponType')">
        <el-radio-group v-model="dataForm.type">
          <el-radio :label="0">{{ $t('coupon.couponType0') }}</el-radio>
          <el-radio :label="1">{{ $t('coupon.couponType1') }}</el-radio>
        </el-radio-group>
      </el-form-item>
      <template v-if="dataForm.type === 0">
        <el-form-item prop="time0" :label="$t('coupon.couponTime0')">
           <el-date-picker
            v-model="dataForm.time0"
            type="datetimerange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期">
          </el-date-picker>
        </el-form-item>
        <el-form-item prop="time1" :label="$t('coupon.couponTime1')">
          <el-date-picker
            v-model="dataForm.time1"
            type="datetime"
            placeholder="选择日期时间">
          </el-date-picker>
        </el-form-item>
      </template>
      <template v-if="dataForm.type === 1">
        <el-form-item prop="time2" :label="$t('coupon.couponTime2')">
          <el-date-picker
            v-model="dataForm.time2"
            type="datetime"
            placeholder="选择日期时间">
          </el-date-picker>
        </el-form-item>
      </template>
      <el-form-item prop="count" :label="$t('coupon.couponCount')">
        <el-input v-model="dataForm.count" :placeholder="$t('coupon.couponCount')"></el-input>
      </el-form-item>
      <el-form-item prop="amount1" :label="$t('coupon.couponAmount1')">
        <el-input v-model="dataForm.amount1" :placeholder="$t('coupon.couponAmount1')"></el-input>
      </el-form-item>
      <el-form-item prop="amount2" :label="$t('coupon.couponAmount2')">
        <el-input v-model="dataForm.amount2" :placeholder="$t('coupon.couponAmount2')"></el-input>
      </el-form-item>
      <el-form-item prop="image" :label="$t('coupon.couponImage')">
        <el-upload
          :action="url"
          :before-upload="beforeUploadHandle"
          :on-success="successHandle"
          list-type="picture-card">
          <i class="el-icon-plus"></i>
        </el-upload>
      </el-form-item>
      <!--<el-form-item prop="detail" :label="$t('coupon.couponDetail')">
        <el-input v-model="dataForm.detail" :placeholder="$t('coupon.couponDetail')"></el-input>
      </el-form-item>-->
      <el-form-item
        v-for="(item, index) in dataForm.detailList"
        :key="item.key"
        :prop="`detailList.${index}.detail`"
        :label="index === 0 ? $t('商品属性') : ''"
        class="resource-list">
        <el-row>
          <el-col :span="4">
            <el-input v-model="item.detail_name" :placeholder="$t('属性名')">
              <!--
                //这里暂时不引入复合型输入框
                <el-select  v-model="item.select"  slot="prepend" placeholder="请选择" >
                <el-option label="上市时间" value="上市时间"></el-option>
                <el-option label="订单号" value="订单号"></el-option>
                <el-option label="用户电话" value="用户电话"></el-option>
                <el-option label="餐厅名" value="餐厅名"></el-option>
                <el-option label="订单号" value="订单号"></el-option>
                <el-option label="用户电话" value="用户电话"></el-option>
                <el-option label="餐厅名" value="餐厅名"></el-option>
                <el-option label="订单号" value="订单号"></el-option>
                <el-option label="用户电话" value="用户电话"></el-option>
                <el-option label="餐厅名" value="餐厅名"></el-option>
                <el-option label="订单号" value="订单号"></el-option>
                <el-option label="用户电话" value="用户电话"></el-option>
              </el-select>-->
            </el-input>
          </el-col>
          <el-col :span="18">
            <el-input v-model="item.detail_info" :placeholder="$t('商品属性内容')">
            </el-input>
          </el-col>
          <el-col :span="2" class="text-center">
            <el-button @click="resourceDeleteHandle(item)" size="small" type="text">{{ $t('delete') }}</el-button>
          </el-col>
        </el-row>
      </el-form-item>
      <el-form-item :label="dataForm.detailList.length <= 0 ? $t('参数') : ''">
        <el-button @click="resourceAddHandle()" class="aui-button--dashed w-percent-100">{{ $t('添加') }}</el-button>
      </el-form-item>
      <el-alert
        title="因商品的特殊性请仔细检查提交数据是否正确，一旦提交不可修改"
        center
        show-icon>
      </el-alert>
    </el-form>
    <template slot="footer">
      <el-button @click="visible = false">{{ $t('cancel') }}</el-button>
      <el-button type="primary" @click="dataFormSubmitHandle()">{{ $t('confirm') }}</el-button>
    </template>
  </el-dialog>
</template>

<script>
import debounce from 'lodash/debounce'
import Cookies from 'js-cookie'
export default {
  data () {
    return {
      url: '',
      visible: false,
      dataForm: {
        id: '',
        name: '',
        time0: [],
        time1: '',
        time2: '',
        type: '',
        platform: '',
        count: '',
        amount1: '',
        amount2: '',
        pic: '',
        url: '',
        detailList: []
      }
    }
  },
  computed: {
    // 效验
    dataRule () {
      return {
        paramCode: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        paramValue: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    init () {
      this.visible = true
      this.url = `${window.SITE_CONFIG['apiURL']}/oss/file/upload?token=${Cookies.get('token')}`
      this.detailList = []
      // this.dataForm.time0 = null
    },
    // 图片上传之前
    beforeUploadHandle (file) {
      if (file.type !== 'image/jpg' && file.type !== 'image/jpeg' && file.type !== 'image/png' && file.type !== 'image/gif') {
        this.$message.error(this.$t('upload.tip', { 'format': 'jpg、png、gif' }))
        return false
      }
    },
    // 图片上传成功
    successHandle (res, file, fileList) {
      if (res.code !== 0) {
        return this.$message.error(res.msg)
      }
      // 将图片的访问地址填充到表单的url字段中
      this.dataForm.url = res.data.url
    },
    // 添加商品属性
    resourceAddHandle () {
      this.dataForm.detailList.push({
        key: new Date().getTime(),
        detail_name: '',
        detail_info: ''
      })
    },
    // 删除商品属性
    resourceDeleteHandle (resource) {
      this.dataForm.detailList = this.dataForm.detailList.filter(item => item.key !== resource.key)
    },
    // 表单提交
    dataFormSubmitHandle: debounce(function () {
      this.$refs['dataForm'].validate((valid) => {
        if (!valid) {
          return false
        }
        this.$http[!this.dataForm.id ? 'post' : 'put']('/shop/coupon/save', this.dataForm).then(({ data: res }) => {
          if (res.code !== 0) {
            return this.$message.error(res.msg)
          }
          this.$message({
            message: this.$t('prompt.success'),
            type: 'success',
            duration: 500,
            onClose: () => {
              this.visible = false
              this.$emit('refreshDataList')
            }
          })
        }).catch(() => {})
      })
    }, 1000, { 'leading': true, 'trailing': false })
  }
}
</script>

<style lang="scss">
.resource-list {
  .el-select .el-input__inner {
    min-width: 110px;
    text-align: center;
  }
}
</style>
