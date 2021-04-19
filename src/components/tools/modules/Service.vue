<template>
  <a-modal title="技术支持" :width="600" :visible="visible" @cancel="handleCancel" :footer="null">
    <a-spin :spinning="false">
      <a-form-item v-bind="formItemLayout" label="开发单位">
        <span class="ant-form-text">湖北协同信安科技有限公司</span>
      </a-form-item>
      <a-form-item v-bind="formItemLayout" label="联系人">
        <span class="ant-form-text">刘佳鑫</span>
      </a-form-item>
      <a-form-item v-bind="formItemLayout" label="联系电话">
        <span class="ant-form-text">13207200719</span>
      </a-form-item>
      <a-form-item v-bind="formItemLayout" label="服务时间">
        <span class="ant-form-text">工作日（8:30-12：00，14:00-17:30）</span>
      </a-form-item>
    </a-spin>
  </a-modal>
</template>

<script>

export default {
  name: "Service",
  data() {
    return {
      formItemLayout: {
        labelCol: {
          xs: { span: 24 },
          sm: { span: 7 }
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 13 }
        }
      },
      visible: false
    };
  },
  methods: {
    add() {
      this.visible = true;
    },
    handleCancel() {
      this.visible = false;
    }
  }
};
</script>