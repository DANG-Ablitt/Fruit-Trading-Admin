<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-sys__params">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-button v-if="$hasPermission('shop:coupon:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-if="$hasPermission('shop:coupon:view')" v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column prop="name" :label="$t('coupon.couponName')" header-align="center" align="center"></el-table-column>
        <el-table-column prop="type" :label="$t('coupon.couponType')" header-align="center" align="center">
          <template slot-scope="scope">
              <el-tag v-if="scope.row.type === 0" size="small" type="success">{{ $t('coupon.couponType0') }}</el-tag>
              <el-tag v-if="scope.row.type === 1" size="small" type="success">{{ $t('coupon.couponType1') }}</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="platform" :label="$t('coupon.couponPlatform')" header-align="center" align="center">
          <template slot-scope="scope">
              <el-tag v-if="scope.row.platform === 0" size="small" type="success">{{ $t('coupon.couponPlatform0') }}</el-tag>
              <el-tag v-if="scope.row.platform === 1" size="small" type="success">{{ $t('coupon.couponPlatform1') }}</el-tag>
              <el-tag v-if="scope.row.platform === 2" size="small" type="success">{{ $t('coupon.couponPlatform2') }}</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="count" :label="$t('coupon.couponCount')" header-align="center" align="center"></el-table-column>
        <el-table-column prop="amount1" :label="$t('coupon.couponAmount1')" header-align="center" align="center"></el-table-column>
        <el-table-column prop="amount2" :label="$t('coupon.couponAmount2')" header-align="center" align="center"></el-table-column>
        <el-table-column prop="memberlevel" :label="$t('coupon.couponMemberLevel')" header-align="center" align="center">
          <template slot-scope="scope">
              <el-tag v-if="scope.row.memberlevel === 0" size="small" type="success">{{ $t('coupon.couponMemberLevel0') }}</el-tag>
              <el-tag v-if="scope.row.memberlevel === 1" size="small" type="success">{{ $t('coupon.couponMemberLevel1') }}</el-tag>
              <el-tag v-if="scope.row.memberlevel === 2" size="small" type="success">{{ $t('coupon.couponMemberLevel2') }}</el-tag>
              <el-tag v-if="scope.row.memberlevel === 3" size="small" type="success">{{ $t('coupon.couponMemberLevel3') }}</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="date" :label="$t('coupon.coupondate')" header-align="center" align="center">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('shop:coupon:info')" type="text" size="small" @click="shop_info(scope.row.id,scope.row.type,scope.row.memberlevel)">{{ $t('coupon.coupondate') }}</el-button>
          </template>
        </el-table-column>
      </el-table>
      <!--显示详细信息的弹窗-->
      <template>
        <el-dialog  title="商品详细信息" :visible.sync="shop_infoVisible" :close-on-click-modal="false" :close-on-press-escape="false">
          <!--商品大图-->
          <span style="font-size: 30px;">商品大图浏览</span>
          <img :src="src" loading="lazy" style="width: 100%;display:block;margin: 10px 0px 10px 0px;">
          <!--商品当前状态-->
          <span style="font-size: 30px;">商品当前状态</span>
          <el-steps :active="active" direction="vertical" style="margin: 10px 0px 10px 0px;">
            <el-step title="未开始" description="管理员已将优惠商品录入系统"></el-step>
            <el-step title="进行中" :description="description"></el-step>
            <el-step title="已结束" description="该商品优惠活动已经结束"></el-step>
          </el-steps>
          <!--商品详细参数-->
          <span style="font-size: 30px;">商品详细信息</span>
          <el-table :data="tableData" style="width:700px;margin: 10px 0px 10px 0px;">
            <el-table-column prop="detail_name" label="商品属性" width="350px"></el-table-column>
            <el-table-column prop="detail_info" label="属性内容" width="350px"></el-table-column>
          </el-table>
        </el-dialog>
      </template>
      <el-pagination
        :current-page="page"
        :page-sizes="[10, 20, 50, 100]"
        :page-size="limit"
        :total="total"
        layout="total, sizes, prev, pager, next, jumper"
        @size-change="pageSizeChangeHandle"
        @current-change="pageCurrentChangeHandle">
      </el-pagination>
      <!-- 弹窗, 新增 -->
      <add-or-update  v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
    </div>
  </el-card>
</template>
<script>
import mixinViewModule from '@/mixins/view-module'
import AddOrUpdate from './coupon-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/shop/coupon/page',
        getDataListIsPage: true,
        deleteIsBatch: true
      },
      shop_infoVisible: false,
      info_url: '',
      // 详细参数列表
      tableData: [],
      data: [],
      // 商品图片地址
      src: '',
      // 活动当前状态
      active: '',
      // 步骤条数据绑定
      description: '',
      dataForm: {
        paramCode: ''
      }
    }
  },
  methods: {
    // 商品详细
    /* eslint-disable */
    shop_info (id,type,memberlevel) {
      this.shop_infoVisible = true
      this.info_url = `${window.SITE_CONFIG['apiURL']}/shop/coupon/${id}`
      this.$nextTick(() => {
        this.$http.get(this.info_url).then(({ data: res }) => {
        // 如果请求失败
        if (res.code !== 0) {
          this.data = []
          return this.$message.error(res.msg)
        }
        // 如果请求成功
        // 获取数据
        this.data = res.data  
        var pp=typeof res.data !== 'object' ? JSON.parse(res.data) : res.data;
        // 商品图片url
        this.src=pp.url
        // 详细参数
        var detail = JSON.parse(pp.detail)
        console.log(detail)
        this.tableData=[]
        // 创建一个新数组
        let arry=[]
        for(var p1 in detail){
          // 日志打印
          console.log(p1)
          console.log(detail.length)
          console.log(detail[p1].detail_name)
          console.log(detail[p1].detail_info)
          // 添加元素
          let p = {
              detail_name: detail[p1].detail_name,
              detail_info: detail[p1].detail_info
          };
          arry.push(p);
        }
        // 将商品品牌加入数组首部
          let pic = {
              detail_name: '品牌',
              detail_info: pp.pic
          };
          arry.unshift(pic)
          this.tableData=arry
          // 步骤条数据绑定
          this.active=memberlevel
          // 秒杀数据绑定
          if(type === 1){
            this.description="秒杀开始时间："+pp.time2
          }else{
            // 预购数据绑定
            var time0 = JSON.parse(pp.time0)
            this.description="抢购开始："+time0.start_time+"     "
                            +"结束："+time0.end_time+"     "+
                            "公布："+pp.time1
          }
        })
      })
    }
  },
  components: {
    AddOrUpdate
  }
}
</script>
