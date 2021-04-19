<template>
  <a-modal :title="title" :width="600" :visible="visible" :confirmLoading="confirmLoading" @ok="handleSubmit" @cancel="handleCancel">
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <a-form-item>
          <a-input v-decorator="['id']" hidden />
        </a-form-item>
        <a-form-item label="标题"  :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="['title', {rules: [{ required: true, message: '请输入标题', whitespace: true}]}]"/>
        </a-form-item>
        <a-form-item label="内容"  :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input type="textarea" :rows="5"  v-decorator="['content', {rules: [{ required: true, message: '请输入内容', whitespace: true}]}]"/>
        </a-form-item>
        <a-form-item label="是否重要"  :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-radio-group v-decorator="['isSign']">
            <a-radio :value="0">否</a-radio>
            <a-radio :value="1">是</a-radio>
          </a-radio-group>
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>
<script>
import { queryService, saveService } from "@/api/manage";

export default {
  props: ["formId","dailyDate","type"],
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
    this.path.edit_path = prefix + "/daily/edit";
    this.path.save_path = prefix + "/daily/save";
  },
  methods: {
    add(id) {
       const that = this;
      that.visible = true;
      that.form.resetFields();
      if (id) {
        that.confirmLoading = true;
        queryService(this.path.edit_path, { id: id }).then((res) => {
          that.form.setFieldsValue(res.result);
          that.confirmLoading = false;
        });
      } else {
        queryService(this.path.edit_path, { id: id }).then((res) => {
          that.form.setFieldsValue(res.result);
          that.confirmLoading = false;
        });     
      }
    },
    handleSubmit() {
      const that = this;
      that.confirmLoading = true;
      that.form.validateFields((errors, values) => {
        if (!errors) {
          values.dailyDate = that.pid;
           if(values.isSign==undefined){
            values.isSign="0";
          }
          values.type="0";
          values.dailyDate = that.dailyDate;
          saveService(that.path.save_path, values)
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
          that.confirmLoading = false;
        }
      });
    },
    handleCancel() {
      this.visible = false;
    },
  },
};
</script>
