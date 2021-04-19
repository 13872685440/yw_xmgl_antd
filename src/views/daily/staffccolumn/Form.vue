<template>
  <a-spin :spinning="spinning">
    <a-card :body-style="{ padding: '24px 32px' }" :bordered="false" title="员工专栏管理" class="card"> 
      <a-form :form="form" class="form" @submit="handleSubmit">
        <a-form-item label="标题" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="['title', {rules: [{ required: true, message: '请输入标题', whitespace: true}]}]"/>
          <a-input hidden v-decorator="['id']" />
        </a-form-item>
        <a-form-item label="内容" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input  type="textarea" :rows="5" v-decorator="['content', {rules: [{ message: '请输入内容', whitespace: true}]}]"/>
        </a-form-item>
        <a-form-item :wrapperCol="{ span: 24 }" style="text-align: center">
          <a-button  htmlType="submit"  type="primary">保存</a-button>
          <a-button style="margin-left: 8px" @click="back">返回</a-button>
        </a-form-item>
      </a-form>
    </a-card>
  </a-spin>
</template>
<script>
import { queryService, back_to_list, save } from "@/api/manage";

export default {
  name: "Staffccolumn_Form",
  props: ["formId","type",],
  data() {
    return {
      form: this.$form.createForm(this),
      spinning: true,
      path: {},
      errordata:{},      
      labelCol:{ lg: { span: 5 }, sm: { span: 5 }},
      wrapperCol:{ lg: {span: 15 }, sm: { span: 17 }}
    };
  },
  created() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.edit_path = prefix + "/staffccolumn/" + this.type;
    this.path.save_path = prefix + "/staffccolumn/save";
  },
  mounted() {
    queryService(this.path.edit_path, { id: this.formId }).then((res) => {
      this.form.setFieldsValue(res.result);
       return res.result;
    });
    this.spinning = false;
  },
  methods: {
    handleSubmit(e) {
      e.preventDefault();
        save(this, this.path.save_path);
    },
    back() {
      back_to_list(this);
    },
  },
};
</script>

<style lang="less" scoped>
.account-settings-info-main {
  width: 100%;
  display: flex;
  height: 100%;
  overflow: auto;

  &.mobile {
    display: block;

    .account-settings-info-left {
      border-right: unset;
      border-bottom: 1px solid #e8e8e8;
      width: 100%;
      height: 50px;
      overflow-x: auto;
      overflow-y: scroll;
    }
    .account-settings-info-right {
      padding: 20px 40px;
    }
  }

  .account-settings-info-left {
    border-right: 1px solid #e8e8e8;
    width: 224px;
  }

  .account-settings-info-right {
    flex: 1 1;
    padding: 8px 40px;

    .account-settings-info-title {
      color: rgba(0, 0, 0, 0.85);
      font-size: 20px;
      font-weight: 500;
      line-height: 28px;
      margin-bottom: 12px;
    }
    .account-settings-info-view {
      padding-top: 12px;
    }
  }
}
</style>
