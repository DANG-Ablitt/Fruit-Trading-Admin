<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-sys__params">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-button v-if="$hasPermission('sys:params:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
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
            <el-button v-if="$hasPermission('shop:coupon:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('coupon.coupondate') }}</el-button>
          </template>
        </el-table-column>
      </el-table>
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
      dataForm: {
        paramCode: ''
      }
    }
  },
  components: {
    AddOrUpdate
  }
}
</script>
