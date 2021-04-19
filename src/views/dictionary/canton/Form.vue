<template>
  <a-spin :spinning="spinning">
    <a-card
      :body-style="{ padding: '24px 32px' }"
      :bordered="false"
      title="行政区划"
      class="card"
    >
      <a-form :form="form" class="form" @submit="handleSubmit">
        <a-form-item
          label="上级区划编码"
          :labelCol="{ lg: { span: 7 }, sm: { span: 7 } }"
          :wrapperCol="{ lg: { span: 5 }, sm: { span: 12 } }"
        >
          <a-input :disabled="true" v-decorator="['superior_code']" />
        </a-form-item>
        <a-form-item
          label="上级区划"
          :labelCol="{ lg: { span: 7 }, sm: { span: 7 } }"
          :wrapperCol="{ lg: { span: 5 }, sm: { span: 12 } }"
        >
          <a-input :disabled="true" v-decorator="['superior_name']" />
        </a-form-item>
        <a-form-item
          label="区划编码"
          :labelCol="{ lg: { span: 7 }, sm: { span: 7 } }"
          :wrapperCol="{ lg: { span: 5 }, sm: { span: 12 } }"
        >
          <a-input v-decorator="['code',{rules: [{ required: true, message: '请输入区划编码', whitespace: true}]}]" />
        </a-form-item>
        <a-form-item
          label="区划名称"
          :labelCol="{ lg: { span: 7 }, sm: { span: 7 } }"
          :wrapperCol="{ lg: { span: 5 }, sm: { span: 12 } }"
        >
          <a-input v-decorator="['name',{rules: [{ required: true, message: '请输入区划名称', whitespace: true}]}]" />
        </a-form-item>
        <a-form-item
          label="扩展字段"
          :labelCol="{ lg: { span: 7 }, sm: { span: 7 } }"
          :wrapperCol="{ lg: { span: 5 }, sm: { span: 12 } }"
        >
          <a-input v-decorator="['extendField']" />
        </a-form-item>

        <a-form-item :wrapperCol="{ span: 24 }" style="text-align: center">
          <a-button htmlType="submit" type="primary">保存</a-button>
          <a-button style="margin-left: 8px" @click="back">返回</a-button>
        </a-form-item>
      </a-form>
    </a-card>
  </a-spin>
</template>
<script>
import { queryService, back_to_list, save } from "@/api/manage";
import { MODEL_SYSTEM } from "@/store/mutation-types";

const editPath = (type) => {
  return "/" + MODEL_SYSTEM + "/canton/" + type;
};
const savePath = "/" + MODEL_SYSTEM + "/canton/save";

export default {
  name: "Canton_Form",
  props: ["formId", "type"],
  data() {
    return {
      form: this.$form.createForm(this),
      spinning: true,
    };
  },
  mounted() {
    queryService(editPath(this.type), { id: this.formId }).then((res) => {
      this.form.setFieldsValue(res.result);
    });
    this.spinning = false;
  },
  methods: {
    handleSubmit(e) {
      e.preventDefault();
      save(this, savePath);
    },
    back() {
      back_to_list(this);
    },
  },
};
</script>

<style lang="less" scoped>
.card {
  margin-bottom: 24px;
}
</style>