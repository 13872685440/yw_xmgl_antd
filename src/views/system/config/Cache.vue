<template>
  <a-spin :spinning="spinning">
    <a-card
      :body-style="{ padding: '24px 32px' }"
      :bordered="false"
      title="系统配置"
      class="card"
    >
      <a-form :form="form" class="form" @submit="handleSubmit">
        <a-form-item
          label="重载query"
          :labelCol="{ lg: { span: 7 }, sm: { span: 7 } }"
          :wrapperCol="{ lg: { span: 5 }, sm: { span: 12 } }"
        >
          <a-switch v-decorator="['reload', { valuePropName: 'checked' }]" />
        </a-form-item>
        <a-form-item
          label="重载namequery"
          :labelCol="{ lg: { span: 7 }, sm: { span: 7 } }"
          :wrapperCol="{ lg: { span: 10 }, sm: { span: 17 } }"
        >
          <a-switch v-decorator="['namequeryreload', { valuePropName: 'checked' }]" />
        </a-form-item>
        <a-form-item
          label="白名单"
          :labelCol="{ lg: { span: 7 }, sm: { span: 7 } }"
          :wrapperCol="{ lg: { span: 10 }, sm: { span: 17 } }"
        >
          <a-textarea
            v-decorator="['whitelist']"
            placeholder="请输入白名单"
            :auto-size="{ minRows: 3 }"
          />
        </a-form-item>
        <a-form-item :wrapperCol="{ span: 24 }" style="text-align: center">
          <a-button htmlType="submit" type="primary" v-action:SYS_ADMINISTRATOR
            >保存</a-button
          >
        </a-form-item>
      </a-form>
    </a-card>
  </a-spin>
</template>
<script>
import { queryService, save } from "@/api/manage";
import { MODEL_SYSTEM } from "@/store/mutation-types";

export default {
  name: "Cache_Form",
  data() {
    return {
      form: this.$form.createForm(this),
      spinning: true,
      path: {},
    };
  },
  mounted() {
    this.edit_path = "/" + MODEL_SYSTEM + "/system/config/edit";
    this.save_path = "/" + MODEL_SYSTEM + "/system/config/save";
    queryService(this.edit_path, {}).then((res) => {
      this.form.setFieldsValue(res.result);
    });
    this.spinning = false;
  },
  methods: {
    handleSubmit(e) {
      e.preventDefault();
      save(this, this.save_path);
      this.spinning = false;
    },
  },
};
</script>

<style lang="less" scoped>
.card {
  margin-bottom: 24px;
}
</style>
