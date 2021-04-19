<template>
  <a-modal :title="title" :width="600" :visible="visible" :confirmLoading="confirmLoading" @ok="handleSubmit" @cancel="handleCancel">
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <a-form-item>
          <a-input v-decorator="['id']" hidden />
        </a-form-item>
        <a-form-item>
          <a-input v-decorator="['customer_id']" hidden />
        </a-form-item>
        <a-form-item label="公司名称"  :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="['corporatName', {rules: [{ required: true, message: '请输入公司名称', whitespace: true}]}]"/>
        </a-form-item>
        <a-form-item label="合作规模"  :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="['cooperationScale', {rules: [{ required: true, message: '请输入合作规模', whitespace: true}]}]"/>
        </a-form-item>
        <a-form-item label="合作方式"  :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="['cooperationMode', {rules: [{ required: true, message: '请输入合作方式', whitespace: true}]}]"/>
        </a-form-item>
        <a-form-item label="评估"  :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="['assess', {rules: [{ required: true, message: '请输入评估', whitespace: true}]}]"/>
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>
<script>
import { queryService, saveService } from "@/api/manage";

export default {
  props: ["formId","pid","type"],
  data() {
    return {
      labelCol: {
        xs: { span: 24 },
        sm: { span: 7 },
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 13 },
      },
      visible: false,
      confirmLoading: false,
      form: this.$form.createForm(this),
      path: {},
      title: "",
      STATES:[]
    };
  },
  created() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.edit_path = prefix + "/partners/edit";
    this.path.save_path = prefix + "/partners/save";
  },
  methods: {
    add(id) {
      this.visible = true;
      if (id) {
        this.confirmLoading = true;
        queryService(this.path.edit_path, { id: id }).then((res) => {
          this.form.setFieldsValue(res.result);
          this.confirmLoading = false;
        });
      } else {
        this.form.resetFields();
        this.form.setFieldsValue({customer_id:this.pid});

      }
    },
    handleSubmit() {
      const that = this;
      this.confirmLoading = true;
      this.form.validateFields((errors, values) => {
        if (!errors) {
          values.customer_id = this.pid;
          saveService(this.path.save_path, values)
            .then((res) => {
              console.log(res.code);
              if (res.code == "400") {
                that.$notification.error({
                  message: res.message,
                  description: res.result,
                });
              } else {
                that.$notification.success({
                  message: "保存成功",
                  description: "",
                });
              }
              that.$emit("ok");
              that.visible = false;
              that.confirmLoading = false;
            })
            .catch((error) => {
              that.confirmLoading = false;
              console.log(error);
            });
        } else {
          this.confirmLoading = false;
        }
      });
    },
    handleCancel() {
      this.visible = false;
    },
  },
};
</script>
