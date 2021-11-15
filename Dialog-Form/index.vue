<template>
  <el-dialog :title="dialogTitle"
             :visible.sync="dialogVisible"
             :close-on-click-modal="false"
             width="550px"
             class="dialogClass"
             :before-close="handleCloseDialog">
    <el-form ref="myform"
             :rules="rules"
             :model="formModel"
             :size="formSize">
      <el-col :span="colSpan">
        <template v-for="(item,index) in formElement">
          <el-form-item :key="index"
                        :label="item.label"
                        :prop="item.prop"
                        :label-width="labelWidth">
            <!-- 输入框 -->
            <el-input clearable
                      :disabled="item.disabled"
                      :show-password="item.showPassword"
                      v-if="item.type==='input'"
                      v-model="formModel[item.prop]"
                      @change="item.change?item.change():''"
                      @blur="item.blur?item.blur($event):''"
                      :placeholder="item.placeholder"></el-input>
            <!-- 占位-->
            <el-input v-if="item.type==='none'"
                      style="visibility: hidden;"></el-input>
            <!-- id-->
            <el-input clearable
                      v-if="item.type==='id'"
                      v-model="formModel[item.prop]"
                      @change="item.change?item.change():''"
                      :placeholder="item.placeholder"
                      style="display:none;"></el-input>
            <!-- 文本域 -->
            <el-input clearable
                      v-if="item.type==='textarea'"
                      type="textarea"
                      :rows="item.rows?item.rows:5"
                      v-model="formModel[item.prop]"
                      :placeholder="item.placeholder"></el-input>
            <!-- 选择器    带搜索 filterable  多选multiple-->
            <el-select v-if="item.type==='select'"
                       v-model="formModel[item.prop]"
                       clearable
                       :filterable="item.filterable"
                       :multiple="item.multiple"
                       :placeholder="item.placeholder"
                       @blur="item.blur?item.blur($event,formModel[item.prop]):''"
                       @change="item.change?item.change(item):''">
              <el-option v-for="(selectedItem,index) in item.typeselects"
                         :key="index"
                         :label="selectedItem.label"
                         :value="selectedItem.value">
              </el-option>
              <!-- :label="item.selectLabel?selectedItem[selectLabel]: selectedItem.label"
                         :value="item.selectValue?selectedItem[selectValue]: selectedItem.value"> -->
            </el-select>
            <!-- 级联选择器 -->
            <!-- :show-all-levels="false" 仅显示最后一级 -->
            <el-cascader v-if="item.type==='cascader'"
                         v-model="formModel[item.prop]"
                         clearable
                         :disabled="item.disabled"
                         :filterable="item.filterable"
                         :show-all-levels="item.levels"
                         :props="{ checkStrictly: item.check,label:item.propsLabel?item.propsLabel:'label',value:item.propsValue?item.propsValue:'label' }"
                         :placeholder="item.placeholder"
                         :options="item.options"
                         @change="item.change?item.change(item):''"></el-cascader>
            <!-- 日期时间选择器 -->
            <!-- 选传： format日期显示格式  默认0年1月2日  0.1.2 ,  valueFormat存储格式见 https://element.eleme.cn/#/zh-CN/component/date-picker#ri-qi-ge-shi 时间戳timestamp  -->
            <el-date-picker v-if="item.type==='date'"
                            v-model="formModel[item.prop]"
                            :type="item.dateType"
                            :placeholder="item.placeholder"
                            :picker-options="item.pickerOptions"
                            :format="item.format"
                            :value-format="item.valueFormat">
            </el-date-picker>

          </el-form-item>
        </template>
      </el-col>
    </el-form>
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
      formModel: {},
      dialogVisible: false
    }
  },
  props: {
    labelWidth: {
      type: String,
      default: "80px"
    },
    formSize: {
      type: String,
      default: "small"
    },
    colSpan: {
      type: Number,
    },
    rules: Object,
    formElement: { //表单元素
      type: Array,
      default: () => []
    },
    rowData: Object,
    dialogTitle:String
  },
  watch: {
    rowData: {
      deep: true,
      immediate: true,
      handler (val) {
        this.formModel = val
      }
    }
  },
  methods: {
    validateForm () {
      let flag = null
      this.$refs['myform'].validate((valid) => {
        if (valid) {
          flag = true
        } else {
          flag = false
        }
      })
      return flag
    },
    handleCloseDialog () {
      this.dialogVisible = false
    },
    handleSubmit () {
      let flag = this.validateForm();
      flag && this.$emit("handleSubmit", this.formModel)
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