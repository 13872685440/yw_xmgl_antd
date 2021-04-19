<template>
  <a-spin :spinning="spinning">
    <a-card :body-style="{padding: '24px 32px'}" :bordered="false" class="card">
      <a-form :form="form" class="form" @submit="handleSubmit">
        <a-row class="form-row" :gutter="16">
         <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="采购合同单号">
              <a-input  @blur.prevent="onchange_unique" v-decorator="['coding', {rules: [{ required: true, message: '请输入销售合同单号', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
           <a-col  :xl="12" :lg="24" :md="24" :sm="24" >
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
          <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="对应项目">
              <a-input v-decorator="['projectName', {rules: [{ required: true, message: '请输入对应项目', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
           <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="供应商名称">
              <a-input v-decorator="['supplierName', {rules: [{ required: true, message: '请输入供应商名称', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
           <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="采购合同金额">
              <a-input v-decorator="['money',validateForm.money]"/>
            </a-form-item>
          </a-col>
            <a-col :xl="{span: 12}" :lg="{span: 12}" :md="{span: 12}" :sm="24">
            <a-form-item label="合同质保期">
              <a-date-picker format="YYYY-MM-DD" v-decorator="['warranty',{rules: [{ type: 'object', required: true, message: '请选择日期!', whitespace: true }]}]"  style="width: 100%" />
            </a-form-item>
          </a-col>
           <a-col :xl="{span: 12}" :lg="{span: 12}" :md="{span: 12}" :sm="24">
            <a-form-item label="最后一次收款时间">
              <a-date-picker format="YYYY-MM-DD" v-decorator="['lastfkdate',{rules:[{type: 'object',required:true,message: '请选择日期', whitespace: true}]}]"  style="width: 100%"/>
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
import moment from "moment";


const savePath = "/" + MODEL_SYSTEM + "/equipment/save";
const getXshtPath = "/" + MODEL_SYSTEM + "/contract/getListByCoding";
const findPath = "/" + MODEL_SYSTEM + "/contract/find";
const getUniquePath = "/" + MODEL_SYSTEM + "/equipment/validationUnique";


export default {
  name: "Equipment_Form",
  props: ["formId", "type", "base"],
  data() {
    return {
      form: this.$form.createForm(this), 
      spinning: true,
      memberLoading: false,
      path: {},
      data: [],
      xshtData:[],
      xsht_id: undefined,
      errordata:{},
      formItemLayout: {
        labelCol: { span: 3 },
      },
      validateForm:{
        money :{ // 金额   必填 数字 限制大小  小数点后只能两位小数
          rules: [{
            required: true,
            validator: (rule, value, cbfn) => {
              value = value.toString();
              if (!value) {
                cbfn('请输入金额')
              } else if (!value.trim().length) {
                cbfn('请输入金额')
              } else if (isNaN(value)) {
                cbfn('请输入正确的金额')
              } else if (Number(value) > Number(this.avalible)) {
                cbfn('金额已超出可用金额')
              } else if (value.indexOf('.') > -1 && value.toString().split('.')[1].length > 2) {
                cbfn('请输入正确的金额，小数点后保留两位数字')
              }
              cbfn()
            }
          }]
        }
      }
    };
  },
  mounted() {
    this.form.setFieldsValue(this.base);
    this.form.setFieldsValue({
      warranty: moment(this.base.warranty),
      lastfkdate: moment(this.base.lastfkdate)
    });
    this.spinning = false;
  },
  methods: {
     onchange_unique(e) {
      const { id, value } = e.target;
      var error = "采购合同号已存在";
      const data = {
        key: id,
        value: value,
        error: error,
      };
      this.memberLoading = true;
      this.errordata = {};
      queryService(getUniquePath, data).then((res) => {
        if (res.result && res.result.error) {
          this.errordata[id] = {
            value: value,
            errors: [
              {
                message: res.result.error,
                field: id,
              },
            ],
          };
          this.form.setFields(this.errordata);
        }
        this.memberLoading = false;
      });
    },
    onck(){
       this.xsht_id = undefined;
    },
     //输入框事件
    handleSearch(keyword) {
       var that=this;
       this.xsht_id = undefined;
      queryService(getXshtPath, {keyword:keyword,isRelation:1}).then((res) => {
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
          projectName:res.result.projectName,
          money:res.result.money,
          xsht_coding:res.result.coding,
          xsht_id:res.result.id});
      });
      this.xsht_id=value;
    },
     handleSubmit(e) {
      e.preventDefault();
      var that=this;
      that.form.validateFields((err, values) => {
        if (!err) {
          if(that.errordata.coding!=undefined){
            that.form.setFields(that.errordata);
          }else{
            values.id=this.formId;
            values.warranty = moment(values.warranty).format("YYYY-MM-DD");
            values.lastfkdate = moment(values.lastfkdate).format("YYYY-MM-DD");
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
          }
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
