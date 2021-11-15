<template>
  <div class="test-view">
    <basics-table :data="tableData"
                  :header="useConfig.getHeader()"
                  v-loading="loading"
                  :searchFormElement="searchFormElement"
                  :params="tableParams"
                  @handleAdd="handleAdd"
                  @handleRefresh="getDataList"
                  @handleSubmitSearch="handleSubmitSearch">
    </basics-table>
    <basics-pagination :total="pageTotal"
                       @handlePageParams="handlePageParams"
                       @handleSizeChange="handleSizeChange"
                       @handleCurrentChange="handleCurrentChange"></basics-pagination>
    <dialog-form ref="dialogForm"
                 :formElement="formElement"
                 :rowData="rowData"
                 :rules="rules"
                 :dialogTitle="dialogTitle"
                 @handleSubmit="handleSubmit"></dialog-form>
  </div>
</template>

<script>
import useConfig from './config';
import useHttp from "@/utils/http"
export default {
  data () {
    return {
      tableData: [],
      tableParams: {
        pageNum: null,
        pageSize: null
      },
      pageTotal: null,
      rowData: {},
      rules: {},
      dialogTitle: '',
      loading: true,
    }
  },
  computed: {
    useConfig () {
      return useConfig(this)
    },
    formElement () {
      return this.useConfig.getFormElement()
    },
    searchFormElement () {
      return this.useConfig.getSearchElement()
    },
    useUserHttp () {
      return useHttp('user', this)
    }
  },
  mounted () {
    this.getDataList()
    this.findAllRoleList()
  },
  methods: {
    /** 获取user数据 */
    getDataList (params = this.tableParams) {
      this.loading = true
      this.useUserHttp.getPageList(params, (result) => {
        this.tableData = result.dataList
        this.pageTotal = result.total
        setTimeout(() => {
          this.loading = false
        }, 500)
      })
    },
    handlePageParams (val) {
      this.tableParams.pageNum = val.currentPage
      this.tableParams.pageSize = val.pageSize
    },
    /** 分页变动 */
    handleSizeChange (val) {
      this.tableParams.pageSize = val
      this.getDataList()
    },
    /** 分页变动 */
    handleCurrentChange (val) {
      this.tableParams.pageNum = val
      this.getDataList()
    },

    handleSubmit (data) {
      if (this.dialogTitle == '编辑') {
        this.useUserHttp.updateData(data)
      } else if (this.dialogTitle == '新增') {
        this.useUserHttp.addData(data)
      }
    },

    handleOpenDialog (data, title) {
      this.dialogTitle = title
      this.rowData = data
      this.$refs.dialogForm.handleOpen()
    },

    handleCloseDialog () {
      this.getDataList()
      this.$refs.dialogForm.handleCloseDialog()
    },

    handleAdd () {
      this.formElement[1].disabled = false
      this.handleOpenDialog({}, '新增')
    },
    /**点击修改按钮 */
    handleUpdate (data) {
      this.formElement[1].disabled = true
      this.handleOpenDialog(data, '编辑')
    },
    /**点击表格删除--异步请求 */
    handleDelete (data) {
      this.useUserHttp.deleteData(data, 'idUser')
    },
    /**获取所有角色 */
    findAllRoleList () {
      useHttp('role').getAllData((result) => {
        let arr = result.dataList.map(item => {
          return { label: item.roleName, value: item.roleField }
        })
        for (let item of this.formElement) {
          if (item.label === '角色') {
            item.typeselects = arr
          }
        }
      })
    },
    handleSubmitSearch (data) {
      this.getDataList({ ...this.tableParams, ...data })
    }
  }
}
</script>
<style lang='scss' scoped>
</style>