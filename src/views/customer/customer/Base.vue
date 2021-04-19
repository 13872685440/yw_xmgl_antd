<template>
  <a-spin :spinning="spinning">
    <a-card :body-style="{padding: '24px 32px'}" :bordered="false" class="card">
      <a-form :form="form" class="form" @submit="handleSubmit">
        <a-row class="form-row" :gutter="16">
          <a-col :xl="12" :lg="12" :md="12" :sm="24">
            <a-form-item label="公司名称">
              <a-input v-decorator="['corporatName', {rules: [{ required: true, message: '请输入公司名称', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="12" :md="12" :sm="24">
            <a-form-item label="所属业务员">
              <a-input v-decorator="['salesman', {rules: [{ required: true, message: '请输入所属业务员', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="12" :md="12" :sm="24">
            <a-form-item label="地址">
              <a-input v-decorator="['addr', {rules: [{ required: true, message: '请输入地址', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="12" :md="12" :sm="24">
            <a-form-item label="注册资金">
              <a-input v-decorator="['registerCapital', {rules: [{ required: true, message: '请输入注册资金', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="12" :md="12" :sm="24">
            <a-form-item label="成立日期">
              <a-input v-decorator="['establishDate', {rules: [{ required: true, message: '请输入地址', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="12" :md="12" :sm="24">
            <a-form-item label="法定代表人">
              <a-input v-decorator="['legalPerson', {rules: [{ required: true, message: '请输入法定代表人', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="12" :md="12" :sm="24">
            <a-form-item label="企业网站">
              <a-input v-decorator="['enterpriseWebsite', {rules: [{ required: true, message: '请输入企业网站', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="12" :md="12" :sm="24">
            <a-form-item label="企业规模（人数）">
              <a-input v-decorator="['enterpriseScale', {rules: [{ required: true, message: '请输入地址', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="12" :md="12" :sm="24">
            <a-form-item label="所属行业">
              <a-input v-decorator="['industry', {rules: [{ required: true, message: '请输入所属行业', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="12" :md="12" :sm="24">
            <a-form-item label="在所属行业位置">
              <a-input v-decorator="['industryPosition', {rules: [{ required: true, message: '请输入在所属行业位置', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="12" :md="12" :sm="24">
            <a-form-item label="客户分级">
              <a-input v-decorator="['customerRating', {rules: [{ required: true, message: '请输入客户分级', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="24" :lg="24" :md="24" :sm="24">
            <a-form-item label="主要业务方向">
              <a-input  type="textarea" v-decorator="['businessDirection']"/>
            </a-form-item>
          </a-col>
          <a-col :xl="24" :lg="24" :md="24" :sm="24">
            <a-form-item label="相关成功案例">
              <a-input  type="textarea" v-decorator="['successCase']"/>
            </a-form-item>
          </a-col>
          <a-col :xl="24" :lg="24" :md="24" :sm="24">
            <a-form-item label="与我司相关业务范畴">
              <a-input  type="textarea" v-decorator="['businessScope']"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="12" :md="12" :sm="24">
            <a-form-item label="客户年AV采购额预估">
              <a-input v-decorator="['avEstimate']"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="12" :md="12" :sm="24">
            <a-form-item label="客户从我司采购额预估">
              <a-input v-decorator="['myEstimate']"/>
            </a-form-item>
          </a-col>
          <a-col :xl="24" :lg="24" :md="24" :sm="24">
            <a-form-item label="近期合作目标">
              <a-input  type="textarea" v-decorator="['nearCooTtarget']"/>
            </a-form-item>
          </a-col>
          <a-col :xl="24" :lg="24" :md="24" :sm="24">
            <a-form-item label="远期合作目标">
              <a-input  type="textarea" v-decorator="['farCooTtarget']"/>
            </a-form-item>
          </a-col>
          <a-col :xl="24" :lg="24" :md="24" :sm="24">
            <a-form-item label="公司组织架构图">
              <a-input  type="textarea"  v-decorator="['framework']"/>
            </a-form-item>
          </a-col>
          <a-col :xl="24" :lg="24" :md="24" :sm="24">
            <a-form-item label="备注">
              <a-input type="textarea" :rows="5" v-decorator="['remark']"/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-form-item :wrapperCol="{ span: 24 }" style="text-align: center">
          <a-button  htmlType="submit"  type="primary">保存</a-button>
        </a-form-item>
      </a-form>
    </a-card>
  </a-spin>
</template>
<script>
import { back_to_list, saveService } from "@/api/manage";
import { MODEL_SYSTEM } from "@/store/mutation-types";

const savePath = "/" + MODEL_SYSTEM + "/customer/save";

export default {
  name: "Customer_Form",
  props: ["formId", "type", "base"],
  data() {
    return {
      form: this.$form.createForm(this), 
      spinning: true,
      memberLoading: false,
      path: {},
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
