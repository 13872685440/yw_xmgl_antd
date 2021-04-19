<template>
  <a-spin :spinning="spinning">
    <a-card :body-style="{padding: '24px 32px'}" :bordered="false" class="card">
      <a-form :form="form" class="form" @submit="handleSubmit">
        <a-row class="form-row" :gutter="16">
         <a-col  v-if="formId!=''"  :xl="24" :lg="24" :md="24" :sm="24">
            <a-form-item label="售后单号">
              <a-input v-decorator="['coding']" :disabled="true"/>
            </a-form-item>
          </a-col>
           <a-col  :xl="24" :lg="24" :md="24" :sm="24" >
            <a-form-item label="关联销售合同号">
              <a-select v-if="base.xsht_id==undefined" show-search show-arrow 
                placeholder="请输入关键词" style="width: 100%"
                :value="xsht_id" :default-active-first-option="false" :filter-option="false" :not-found-content="null"
                @search="handleSearch" @change="handleChange"> 
                <a-icon slot="suffixIcon" type="close"  @click="onck"  />
                <a-select-option v-for="d in xshtData" :key="d.value">{{ d.text }}</a-select-option>
              </a-select>
              <a-input hidden v-decorator="['xsht_id']" />
              <a-input hidden v-decorator="['xsht_coding']" />
              <a-input v-if="base.xsht_id!=undefined"  :disabled="true" v-decorator="['xsht_coding']" />
            </a-form-item>
          </a-col>
          <a-col :xl="24" :lg="24" :md="24" :sm="24">
            <a-form-item label="对应项目">
              <a-input v-decorator="['projectName', {rules: [{ required: true, message: '请输入对应项目', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="24" :lg="24" :md="24" :sm="24">
            <a-form-item label="性质">
              <a-select placeholder="请选择性质" v-decorator="['nature_code',{ rules: [{required: true,message: '请选择性质',whitespace: true,}]}]" >
                <a-select-option v-for="nature in NATURE" :key="nature.code" :value="nature.code">{{ nature.name }}</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :xl="24" :lg="24" :md="24" :sm="24">
            <a-form-item label="描述">
              <a-input type="textarea" :rows="5" v-decorator="['describe', {rules: [{required: true,  message: '请输入描述', whitespace: true}]}]"/>
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
import { back_to_list, saveService,queryService } from "@/api/manage";
import { MODEL_SYSTEM } from "@/store/mutation-types";

const savePath = "/" + MODEL_SYSTEM + "/aftersales/save";
const getXshtPath = "/" + MODEL_SYSTEM + "/contract/getListByCoding";
const findPath = "/" + MODEL_SYSTEM + "/contract/find";


export default {
  name: "Aftersales_Form",
  props: ["formId", "type", "base","NATURE"],
  data() {
    return {
      form: this.$form.createForm(this), 
      spinning: true,
      memberLoading: false,
      path: {},
      xshtData:[],
      xsht_id: undefined,
    };
  },
  mounted() {
    this.form.setFieldsValue(this.base);
    this.spinning = false;
  },
  methods: {
     //输入框事件
    handleSearch(keyword) {
       var that=this;
       this.xsht_id = undefined;
       queryService(getXshtPath, {keyword:keyword,isRelation:2}).then((res) => {
        that.xshtData = [];
         res.result.forEach((value) => {
           that.xshtData.push({
              value:value.id,
              text:value.coding,
            });
          });
      });
    },
     handleChange(value) {
        queryService(findPath, {id:value}).then((res) => {
        this.form.setFieldsValue({
          xsht_coding:res.result.coding,
          xsht_id:res.result.id});
      });
      this.xsht_id=value;
    },
    onck(){
       this.xsht_id = undefined;
    },
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
