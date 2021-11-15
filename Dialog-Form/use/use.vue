<template>
  <dialog-form ref="dialogForm"
               :formElement="formElement"
               :rowData="rowData"
               :rules="rules"
               :dialogTitle="dialogTitle"
               @handleSubmit="handleSubmit"></dialog-form>
</template>

<script>
import dialogForm from '@/components/Dialog-Form';
export default {
  components: { dialogForm },
  data () {
    return {
      formElement: [
        {
          type: 'input',
          label: '城市',
          prop: 'city'
        }
      ],
      rules: {
        city: [{ required: true, message: '请输入', trigger: 'blur' }]
      },
      rowData: {},
      dialogTitle: '新增'
    }
  },
  methods: {
    handleSubmit (data) {

    },
    handleOpenDialog (data, title) {
      this.dialogTitle = title
      this.rowData = data
      this.$refs.dialogForm.handleOpen()
    }
  }

}
</script>
<style lang='scss' scoped>
</style>