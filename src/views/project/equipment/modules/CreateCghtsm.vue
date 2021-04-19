<template>
  <a-modal :title="title" :width="600" :visible="visible" :confirmLoading="confirmLoading" @ok="handleSubmit" @cancel="handleCancel">
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <a-form-item label="内容" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input type="textarea" :rows="5" v-decorator="['content', {rules: [{required: true,  message: '请输入内容', whitespace: true}]}]"/>
        </a-form-item>
        <a-form-item label="状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-radio-group defaultValue="00100001" v-decorator="['state_code']">
            <template v-for="ot in STATES">
              <a-radio :key="ot.code" :value="ot.code" >{{ ot.name }}</a-radio>
            </template>
          </a-radio-group>
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>
<script>
import { queryService, saveService } from "@/api/manage";

import { MODEL_SYSTEM_DICTIONARY } from "@/store/mutation-types";


export default {
  props: ["keyValue","type"],
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
      this.path.edit_path = prefix + "/content/edit";
      this.path.save_path = prefix + "/content/save";

      queryService(MODEL_SYSTEM_DICTIONARY, {
        typeCode: "XMSTATE"
      }).then((res) => {
        if (res.code === "400") {
              this.$notification.error({
                message: "参数错误",
                description: "请求出现错误，请联系管理员",
              });
            } else {
              this.STATES = res.result;
            }
          });
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
      }
    },
    handleSubmit() {
      const that = this;
      this.confirmLoading = true;
      this.form.validateFields((errors, values) => {
        if (!errors) {
          values.keyValue = this.keyValue;
          values.type = this.type;
           if(values.state_code==undefined){
            values.state_code="00100001"
          }
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
