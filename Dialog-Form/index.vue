<template>
  <el-dialog :title="dialogTitle"
             :visible.sync="dialogVisible"
             :close-on-click-modal="false"
             width="550px"
             class="dialogClass"
             :before-close="handleCloseDialog">
    <basics-form ref="basicsForm"
                 :showSubmit="false"
                 :rowData="rowData"
                 :rules="rules"
                 :formElement="formElement"> </basics-form>
    <!--弹出框按钮 -->
    <div slot="footer"
         class="dialog-footer">
      <el-button @click="handleCloseDialog"
                 size="small">取 消</el-button>
      <el-button type="primary"
                 size="small"
                 @click="handleSubmit">确 定</el-button>
    </div>
  </el-dialog>

</template>

<script>
export default {
  data () {
    return {
      dialogVisible: false
    }
  },
  props: {
    rules: Object,
    formElement: { //表单元素
      type: Array,
      default: () => []
    },
    rowData: Object,
    dialogTitle: String
  },
  methods: {
    handleCloseDialog () {
      this.dialogVisible = false
    },
    handleSubmit () {
      const formModel = this.$refs.basicsForm.handleSubmit()
      formModel && this.$emit("handleSubmit", formModel)
    },
    handleOpen () {
      this.dialogVisible = true
    }
  }
}
</script>
<style lang="scss" scoped>
.el-form {
  padding-right: 20px;
  .el-form-item {
    // .el-input {
    //   width: 90%;
    // }
    ::v-deep {
      .el-form-item__label {
        text-overflow: ellipsis;
        overflow: hidden;
        white-space: nowrap;
      }
    }
    .el-select {
      width: 100%;
    }
    .el-cascader {
      width: 100%;
    }
    .el-date-editor.el-input {
      width: 100%;
    }
  }
  ::v-deep {
    // .json-save-btn {
    //   display: none;
    // }
    .jsoneditor {
      height: 400px;
      max-height: 400px;
      overflow: auto;
    }
  }
}
</style>