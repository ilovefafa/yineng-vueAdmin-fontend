<template>
  <div class="main">
    <ceiling-operator>
      <el-button type="primary" @click="queryItem">查询项目</el-button>
      <el-input v-model="queryItemData" clearable placeholder="请输入项目名称" style="width:200px;" ></el-input>
    </ceiling-operator>
    <el-table
      height="475"
      :data="item"
      style="width: 100%"
    >
      <el-table-column type="expand">
        <template slot-scope="props">
          <el-form label-position="left" inline class="table-expand">
            <el-row>
              <el-col :span="8">
                <el-form-item label="创建日期">
                  <span>{{ props.row.date }}</span>
                </el-form-item>
                <el-form-item label="项目名称">
                  <span>{{ props.row.name }}</span>
                </el-form-item>
                <el-form-item label="客户名称">
                  <span>{{ props.row.client }}</span>
                </el-form-item>
                <el-form-item label="联系方式">
                  <span>{{ '无' }}</span>
                </el-form-item>
              </el-col>
              <el-col :span="8">
                <el-form-item label="报价范围">
                  <span>{{ props.row.scope }}</span>
                </el-form-item>
                <el-form-item label="品牌指定">
                  <span>{{ props.row.brand }}</span>
                </el-form-item>
                <el-form-item label="业务员">
                  <span>{{ props.row.salesman }}</span>
                </el-form-item>
                <el-form-item label="技术负责人">
                  <span>{{ props.row.technicalDirector }}</span>
                </el-form-item>
              </el-col>
              <el-col :span="8">
                <el-form-item label="收货人信息">
                  <span>{{ props.row.consignee }}</span>
                </el-form-item>
                <el-form-item label="收货地址">
                  <span>{{ props.row.address }}</span>
                </el-form-item>
                <el-form-item label="失效原因">
                  <span>{{ props.row.failureReason=='其他' ?  props.row.otherFailureReason :props.row.failureReason}}</span>
                </el-form-item>
                <el-form-item label="备注">
                  <span>{{ props.row.remarks }}</span>
                </el-form-item>
              </el-col>
            </el-row>
          </el-form>
        </template>
      </el-table-column>
      <el-table-column
        prop="ynID"
        label="项目编号"
        sortable
      >
      </el-table-column>
      <el-table-column
        prop="name"
        label="项目名称"
      >
      </el-table-column>
      <el-table-column
        prop="salesman"
        label="业务员">
      </el-table-column>
      <el-table-column
        prop="technicalDirector"
        label="技术负责人">
      </el-table-column>
      <el-table-column
        prop="offerExpirationDate"
        label="报价期限">
      </el-table-column>
      <el-table-column
        prop="manufactureExpirationDate"
        label="生产期限">
      </el-table-column>
      <el-table-column
        prop="status"
        label="进度"
        :filters="statusFilters"
        :filter-method="filterTag"
        filter-placement="bottom-end">
        <template slot-scope="scope">
          <el-tag
            :type="tagDistinguish(scope.row.status)"
            disable-transitions>
            {{scope.row.status}}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column label="操作" min-width="90px" v-if="operatePermisson">
        <template slot-scope="scope">
          <el-button
            size="mini"
            type="primary"
            @click="handleConfirm(scope.$index, scope.row)">
            确认
          </el-button>
          <el-button
            size="mini"
            @click="handleEdit(scope.$index, scope.row)">
            编辑
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-dialog title="状态确认" :visible.sync="dialogFormVisible" width='360px' top='2vh' class="dialog">
      <el-form :model="singleItem">
        <el-form-item label="报价完成" :label-width="formLabelWidth">
          <el-switch
            v-model="singleItem.confirm.offerConfirm"
            active-color="#13ce66"
            inactive-color="#ff4949">
          </el-switch>
          <span>{{singleItem.confirm.offerConfirmDate}}</span>
        </el-form-item>
        <el-form-item label="合同签订" :label-width="formLabelWidth">
          <el-switch
            v-model="singleItem.confirm.orderConfirm"
            active-color="#13ce66"
            inactive-color="#ff4949">
          </el-switch>
          <span>{{singleItem.confirm.orderConfirmDate}}</span>

        </el-form-item>
        <el-form-item label="设计完成" :label-width="formLabelWidth">
          <el-switch
            v-model="singleItem.confirm.techConfirm"
            active-color="#13ce66"
            inactive-color="#ff4949">
          </el-switch>
          <span>{{singleItem.confirm.techConfirmDate}}</span>

        </el-form-item>
        <el-form-item label="采购完成" :label-width="formLabelWidth">
          <el-switch
            v-model="singleItem.confirm.purchasingConfirm"
            active-color="#13ce66"
            inactive-color="#ff4949">
          </el-switch>
          <span>{{singleItem.confirm.purchasingConfirmDate}}</span>

        </el-form-item>
        <el-form-item label="物料到齐" :label-width="formLabelWidth">
          <el-switch
            v-model="singleItem.confirm.warehouseConfirm"
            active-color="#13ce66"
            inactive-color="#ff4949">
          </el-switch>
          <span>{{singleItem.confirm.warehouseConfirmDate}}</span>
        </el-form-item>
        <el-form-item label="生产完成" :label-width="formLabelWidth">
          <el-switch
            v-model="singleItem.confirm.productConfirm"
            active-color="#13ce66"
            inactive-color="#ff4949">
          </el-switch>
          <span>{{singleItem.confirm.productConfirmDate}}</span>
        </el-form-item>
        <el-form-item label="送货中" :label-width="formLabelWidth">
          <el-switch
            v-model="singleItem.confirm.shipmentConfirm"
            active-color="#13ce66"
            inactive-color="#ff4949">
          </el-switch>
          <span>{{singleItem.confirm.shipmentConfirmDate}}</span>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="updateConfirm()">确 定</el-button>
        <el-button @click="dialogFormVisible = false">取 消</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import moment from 'moment'
import ceilingOperator from '@/components/ceilingOperator'
// import func from './vue-temp/vue-editor-bridge'
export default {
  components: {
    ceilingOperator
  },
  data() {
    return {
      formLabelWidth: '100px',
      dialogFormVisible: false,
      item: [],
      navBarFixed: false,
      queryItemData: '',
      statusFilters: [
        {
          text: '失效',
          value: '失效'
        },
        {
          text: '项目完成',
          value: '项目完成'
        }
      ],
      singleItem: {
        confirm: {
          offerConfirm: false,
          orderConfirm: false,
          techConfirm: false,
          purchasingConfirm: false,
          warehouseConfirm: false,
          productConfirm: false,
          shipmentConfirmDate: '',
          offerConfirmDate: '',
          orderConfirmDate: '',
          techConfirmDate: '',
          purchasingConfirmDate: '',
          warehouseConfirmDate: '',
          productConfirmDate: ''
        }
      }
    }
  },
  mounted() {
    this.getItem()
  },
  watch: {
    queryItemData(newVal, oldVal) {
      if (newVal === '') {
        this.getItem()
      }
    }
  },
  methods: {
    async updateConfirm() {
      const date = moment().format('YYYY-MM-DD  HH:mm:ss')
      if (this.singleItem.confirm.shipmentConfirm === true) {
        this.singleItem.status = '送货中'
        if (!this.singleItem.confirm.shipmentConfirmDate) {
          this.singleItem.confirm.shipmentConfirmDate = date
        }
      } else if (this.singleItem.confirm.productConfirm === true) {
        this.singleItem.status = '生产完成'
      } else if (this.singleItem.confirm.orderConfirm === true) {
        this.singleItem.status = '生产中'
      } else if (this.singleItem.confirm.offerConfirm === true) {
        this.singleItem.status = '报价完成'
      }
      // 加入确认时间
      function confirmDate(confirm1, date1) {
        const confirm = this.singleItem.confirm[confirm1]
        const date = this.singleItem.confirm[date1]
        if (confirm === true && !date) {
          const dateNow = moment().format('YYYY-MM-DD  HH:mm:ss')
          this.singleItem.confirm[date1] = dateNow
        }
      }

      confirmDate.call(this, 'offerConfirm', 'offerConfirmDate')
      confirmDate.call(this, 'orderConfirm', 'orderConfirmDate')
      confirmDate.call(this, 'techConfirm', 'techConfirmDate')
      confirmDate.call(this, 'purchasingConfirm', 'purchasingConfirmDate')
      confirmDate.call(this, 'warehouseConfirm', 'warehouseConfirmDate')
      confirmDate.call(this, 'productConfirm', 'productConfirmDate')
      confirmDate.call(this, 'shipmentConfirm', 'shipmentConfirmDate')
      await this.$request({
        url: 'item/update',
        method: 'post',
        data: {
          item: this.singleItem
        }
      })
      this.dialogFormVisible = false
      this.getItem()
    },
    async handleConfirm(index, row) {
      const req = await this.$request({
        url: 'item/edit',
        method: 'get',
        params: {
          id: row._id
        }
      })
      this.singleItem = req.data.item
      this.dialogFormVisible = true
    },
    tagDistinguish(status) {
      switch (status) {
        case '项目完成': return 'success'
        case '失效': return 'danger'
        case '生产完成': return 'success'
        default: return ''
      }
    },
    filterTag(value, row) {
      return row.status === value
    },
    getItem() {
      this.$request({
        url: 'item/getHistory',
        method: 'get'
      })
        .then(res => {
          this.item = res.data.item
        })
    },
    async queryItem() {
      const res = await this.$request({
        url: 'item/historyQuery',
        method: 'get',
        params: {
          queryItemData: this.queryItemData
        }
      })
      this.item = res.data.item
    },
    handleEdit(index, row) {
      this.$router.push({
        path: `/ProjectManagement/EditItem`,
        query: { id: row._id }
      })
    },
    async handleDelete(index, row) {

    },
    watchScroll() {
      var scrollTop = window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop
      //  当滚动超过 50 时，实现吸顶效果
      if (scrollTop > 50) {
        this.navBarFixed = true
      } else {
        this.navBarFixed = false
      }
    }
  },
  computed: {
    operatePermission() {
      if (this.$store.state.user.roles.indexOf('admin') > -1) {
        return true
      }
      return false
    }
  }
}
</script>

<style lang="scss" scoped>
.main {
  .table-expand {
    font-size: 0;
    label {
      width: 90px;
      color: #99a9bf;
    }
    .el-form-item {
      margin-right: 0;
      margin-bottom: 0;
      // width: 33.333%;
      display: block;
    }
  }
  span {
    margin-left: 10px;
  }
}
</style>
