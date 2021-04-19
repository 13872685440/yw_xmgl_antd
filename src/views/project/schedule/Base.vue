<template>
  <a-spin :spinning="spinning">
    <a-card :body-style="{padding: '24px 32px'}" :bordered="false" class="card">
      <a-form :form="form" class="form" @submit="handleSubmit">
        <a-row class="form-row" :gutter="16">
         <a-col  v-if="formId!=''"  :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="跟单号">
              <a-input v-decorator="['coding']" :disabled="true"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="客户公司名称">
              <a-input v-decorator="['companyName', {rules: [{ required: true, message: '请输入客户公司名称', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
           <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="项目名称">
              <a-input v-decorator="['projectName', {rules: [{ required: true, message: '请输入项目名称', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
           <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="项目负责人">
              <a-input v-decorator="['directorName', {rules: [{ required: true, message: '请输入项目负责人', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
           <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="负责人联系电话">
              <a-input v-decorator="['directorPhone', {rules: [{ required: true, message: '请输入负责人联系电话', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
           <a-col :xl="{span: 12}" :lg="{span: 12}" :md="{span: 12}" :sm="24">
            <a-form-item label="项目来源">
              <a-select v-if="!isview" placeholder="请选择项目来源" v-decorator="['source_code',{ rules: [{required: true,message: '请选择项目来源',whitespace: true,}]}]" >
              <a-select-option v-for="source in SOURCE" :key="source.code" :value="source.code">{{ source.name }}</a-select-option>
            </a-select>
              <a-input v-if="isview" v-decorator="['source_name']"/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-form-item :wrapperCol="{ span: 24 }" style="text-align: center">
          <a-button v-if="!isview"  htmlType="submit"  type="primary">保存</a-button>
        </a-form-item>
      </a-form>
    </a-card>
  </a-spin>
</template>
<script>
import { back_to_list, saveService } from "@/api/manage";
import { MODEL_SYSTEM } from "@/store/mutation-types";

const savePath = "/" + MODEL_SYSTEM + "/schedule/save";


export default {
  name: "Schedule_Form",
  props: ["formId", "type", "base","SOURCE", "isview"],
  data() {
    return {
      form: this.$form.createForm(this), 
      spinning: true,
      memberLoading: false,
      path: {},
      formItemLayout: {
        labelCol: { span: 3 },
      },
    };
  },
  mounted() {
     this.form.setFieldsValue(this.base);
    this.spinning = false;
  },
  methods: {
     handleSubmit(e) {
      e.preventDefault();
      this.form.validateFields((err, values) => {
        values.id=this.formId;
        if (!err) {
          saveService(savePath, values)
            .then((res) => {
              this.$notification.success({
                message: "保存成功",
                description: "",
              });
              this.$emit("changeForm", {
                name: "base",
                data: res.result,
              });
              this.spinning = false;
            })
            .catch(() => {
              this.spinning = false;
            });
        } else {
          this.spinning = false;
        }
      });
    },
    back() {
      back_to_list(this);
    }
  }
};
</script>
