<template>
  <a-spin :spinning="spinning">
    <a-card :body-style="{ padding: '24px 32px' }" :bordered="false" title="出差流程管理" class="card"> 
      <a-form :form="form" class="form" @submit="handleSubmit">
            <a-form-item label="出差计划与目的" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input  type="textarea" :rows="5" v-decorator="['plan', {rules: [{required: true, message: '请输入内容', whitespace: true}]}]"/>
              <a-input hidden v-decorator="['id']" />
            </a-form-item>
            <a-form-item label="行程安排" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input  type="textarea" :rows="5" v-decorator="['scheduling', {rules: [{ required: true,message: '请输入内容', whitespace: true}]}]"/>
            </a-form-item>
                   <a-form-item label="选择同行人员" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-select mode="multiple" size="default" placeholder="请选择同行人员" style="width:100%"
            v-decorator="['colleagues', {rules: [{ required: true, type: 'array', message: '请选择同行人员',whitespace: true}]}]">
            <a-select-option v-for="item in USERS" :key="item.id" :value="item.id" >{{item.name}}</a-select-option >
          </a-select>
        </a-form-item>
            <a-form-item label="出差成果反馈" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input  type="textarea" :rows="5" v-decorator="['feedback', {rules: [{ message: '请输入内容', whitespace: true}]}]"/>
            </a-form-item>
            <a-form-item label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input  type="textarea" :rows="5" v-decorator="['remark', {rules: [{ message: '请输入内容', whitespace: true}]}]"/>
            </a-form-item>
           <!-- <a-col  :xl="{span: 23, offset: 1}" :lg="{span: 24}" :md="{span: 24}" :wrapperCol="{ span: 24 }">
            <a-form-item label="审核人">
              <a-inputv-decorator="['content', {rules: [{ message: '请输入内容', whitespace: true}]}]"/>
            </a-form-item>
          </a-col> -->
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
  name: "Away_Form",
  props: ["formId","type","USERS"],
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
    this.path.edit_path = prefix + "/away/" + this.type;
    this.path.save_path = prefix + "/away/save";
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
