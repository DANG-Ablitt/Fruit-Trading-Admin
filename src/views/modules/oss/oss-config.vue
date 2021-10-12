<template>
  <el-dialog :visible.sync="visible" :title="$t('oss.config')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" label-width="120px">
      <el-form-item :label="$t('oss.type')" size="mini">
        <el-radio-group v-model="dataForm.type">
          <el-radio :label="1">{{ $t('oss.type1') }}</el-radio>
          <el-radio :label="2">{{ $t('oss.type2') }}</el-radio>
          <el-radio :label="3">{{ $t('oss.type3') }}</el-radio>
        </el-radio-group>
      </el-form-item>
      <template v-if="dataForm.type === 1">
        <el-form-item prop="minio_url" :label="$t('oss.minio_url')">
          <el-input v-model="dataForm.minio_url" :placeholder="$t('oss.minio_url')"></el-input>
        </el-form-item>
        <el-form-item prop="minio_name" :label="$t('oss.minio_name')">
          <el-input v-model="dataForm.minio_name" :placeholder="$t('oss.minio_name')"></el-input>
        </el-form-item>
        <el-form-item prop="minio_pass" :label="$t('oss.minio_pass')">
          <el-input v-model="dataForm.minio_pass" :placeholder="$t('oss.minio_pass')"></el-input>
        </el-form-item>
        <el-form-item prop="minio_bucketName" :label="$t('oss.minio_bucketName')">
          <el-input v-model="dataForm.minio_bucketName" :placeholder="$t('oss.minio_bucketName')"></el-input>
        </el-form-item>
      </template>
      <template v-else-if="dataForm.type === 2">
        <span class="el-dropdown-link">
            <img src="~@/assets/img/tips.jpg" style="width=300px;height:300px;display:block;margin:0 auto;">
        </span>
      </template>
      <template v-else-if="dataForm.type === 3">
        <span class="el-dropdown-link">
            <img src="~@/assets/img/tips.jpg" style="width=300px;height:300px;display:block;margin:0 auto;">
        </span>
      </template>
    </el-form>
    <template slot="footer">
      <el-button  v-if="dataForm.type === 1" @click="visible = false">{{ $t('cancel') }}</el-button>
      <el-button  v-if="dataForm.type === 1" type="primary" @click="dataFormSubmitHandle()">{{ $t('confirm') }}</el-button>
    </template>
  </el-dialog>
</template>

<script>
import debounce from 'lodash/debounce'
export default {
  data () {
    return {
      visible: false,
      dataForm: {
        type: 0,
        minio_url: '',
        minio_name: '',
        minio_pass: '',
        minio_bucketName: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        qiniuDomain: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        qiniuAccessKey: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        qiniuSecretKey: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        qiniuBucketName: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        aliyunDomain: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        aliyunEndPoint: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        aliyunAccessKeyId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        aliyunAccessKeySecret: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        aliyunBucketName: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        qcloudDomain: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        qcloudAppId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        qcloudSecretId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        qcloudSecretKey: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        qcloudBucketName: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        qcloudRegion: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        fastdfsDomain: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        localDomain: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        localPath: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ]
      }
    }
  },
  watch: {
    'dataForm.type' (val) {
      this.$refs['dataForm'].clearValidate()
    }
  },
  methods: {
    init () {
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        this.getInfo()
      })
    },
    // 获取信息
    getInfo () {
      this.$http.get('/oss/file/info').then(({ data: res }) => {
        if (res.code !== 0) {
          return this.$message.error(res.msg)
        }
        this.dataForm = res.data
      }).catch(() => {})
    },
    // 表单提交
    dataFormSubmitHandle: debounce(function () {
      this.$refs['dataForm'].validate((valid) => {
        if (!valid) {
          return false
        }
        this.$http.post('/oss/file', this.dataForm).then(({ data: res }) => {
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
