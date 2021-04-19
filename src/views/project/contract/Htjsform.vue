<template>
  <a-spin :spinning="spinning">
    <a-card :body-style="{padding: '24px 32px'}" :bordered="false" class="card">
      <a-form :form="form" class="form" @submit="handleSubmit">
        <a-row class="form-row" :gutter="16">
          <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="销售合同号">
              <a-input hidden v-decorator="['id']" />
              <a-input hidden v-decorator="['c_id']" />
              <a-input :disabled="true" v-decorator="['coding']" />
            </a-form-item>
          </a-col>
          <a-col  :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="项目名称">
              <a-input :disabled="true" v-decorator="['projectName']"/>
            </a-form-item>
          </a-col>
          <a-col :xl="{span: 12}" :lg="24" :md="24" :sm="24">
            <a-form-item label="来源">
              <a-input :disabled="true" v-decorator="['source_name']"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="合同金额" >
            <a-input :disabled="true" v-decorator="['money']"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="销售项目成本">
              <a-input :disabled="true" v-decorator="['salesCost', {rules: [{ required: true, message: '请输入金额'}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="项目利润速算">
              <a-input :disabled="true" v-decorator="['profitVelocity', {rules: [{ required: true, message: '请输入金额'}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="固定成本汇总">
              <a-input :disabled="true"  v-decorator="['fixedCSummary', {rules: [{ required: true, message: '请输入金额'}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="货物成本">
              <a-input @change="onClick"  v-decorator="['goodsCost', {rules: [{ required: true, message: '请输入金额'}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="合同税务成本">
              <a-input @change="onClick"  v-decorator="['contractTaxCost', {rules: [{ required: true, message: '请输入金额'}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="{span: 12}" :lg="{span: 12}" :md="{span: 12}" :sm="24">
            <a-form-item label="浮动成本汇总">
              <a-input :disabled="true"  v-decorator="['floatCSummary']"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="差旅招待成本">
              <a-input @change="onClick" v-decorator="['travelCost', {rules: [{ required: true, message: '请输入金额'}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="货物运输成本">
              <a-input@change="onClick" v-decorator="['transportCost', {rules: [{ required: true, message: '请输入金额'}]}]"/>
            </a-form-item>
          </a-col>
           <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="技术支持成本">
              <a-input @change="onClick" v-decorator="['technologyCost', {rules: [{ required: true, message: '请输入金额'}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="其他成本">
              <a-input @change="onClick" v-decorator="['otherCost', {rules: [{ required: true, message: '请输入金额'}]}]"/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-form-item :wrapperCol="{ span: 24 }" style="text-align: center">
          <a-button v-if="state!='00100002'"  htmlType="submit"  type="primary" style="margin-left: 8px">保存</a-button>
          <a-button v-if="state!='00100002'"  type="primary" style="margin-left: 8px"  @click="saveData">提交</a-button>
        </a-form-item>
      </a-form>
    </a-card>
  </a-spin>
</template>
<script>
import { queryService,back_to_list, saveService} from "@/api/manage";
import { MODEL_SYSTEM } from "@/store/mutation-types";

const findPath = "/" + MODEL_SYSTEM + "/cstatement/find";
const savePath = "/" + MODEL_SYSTEM + "/cstatement/save";

export default {
  name: "cstatement_Form",
  props: ["cid","base","isview","state","refreshkey"],
  data() {
    return {
      form: this.$form.createForm(this), 
      spinning: true,
      memberLoading: false,
      path: {},
      data:[],
      formItemLayout: {
        labelCol: { span: 3 },
      },
    };
  },
   mounted() {
      queryService(findPath, { cid: this.cid }).then(res => {
         this.data = res.result;
        this.data.money =  res.result.money!=undefined?Number(res.result.money):"";
        this.data.salesCost= res.result.salesCost!=undefined?Number(res.result.salesCost):"";
        this.data.profitVelocity=res.result.profitVelocity!=undefined?Number(res.result.profitVelocity):"";
        this.data.fixedCSummary=res.result.fixedCSummary!=undefined?Number(res.result.fixedCSummary):"";
        this.data.goodsCost= res.result.goodsCost!=undefined?Number(res.result.goodsCost):"";
        this.data.contractTaxCost= res.result.contractTaxCost!=undefined?Number(res.result.contractTaxCost):"";
        this.data.floatCSummary= res.result.floatCSummary!=undefined?Number(res.result.floatCSummary):"";
        this.data.travelCost=res.result.travelCost!=undefined?Number(res.result.travelCost):"";
        this.data.transportCost= res.result.transportCost!=undefined?Number(res.result.transportCost):"";
        this.data.technologyCost=res.result.technologyCost!=undefined?Number(res.result.technologyCost):"";
        this.data.otherCost= res.result.otherCost!=undefined?Number(res.result.otherCost):"";
        this.form.setFieldsValue(this.data);
      });
    this.spinning = false;
  },
  methods: {
    onClick(v) {
      let values = v.target;
      if(values.value!=''){
        var data = this.data;
        data.goodsCost = values.id=='goodsCost'?values.value:data.goodsCost;
        data.contractTaxCost = values.id=='contractTaxCost'?values.value:data.contractTaxCost;

        data.travelCost = values.id=='travelCost'?values.value:data.travelCost;
        data.transportCost = values.id=='transportCost'?values.value:data.transportCost;
        data.technologyCost = values.id=='technologyCost'?values.value:data.technologyCost;
        data.otherCost = values.id=='otherCost'?values.value:data.otherCost;

        var gd = "";//固定   
        if(data.goodsCost!=undefined  && data.contractTaxCost!=undefined 
        && data.goodsCost!=""  && data.contractTaxCost!=""){
            gd = Number(data.goodsCost)+Number(data.contractTaxCost);
        }

        var fd = "";//浮动
        if((data.travelCost!=undefined && data.travelCost!="") 
          && (data.transportCost!=undefined && data.transportCost!="")
          && (data.technologyCost!=undefined && data.technologyCost!="") 
          && (data.otherCost!=undefined && data.otherCost!="")){
            fd = Number(data.travelCost)+Number(data.transportCost)+Number(data.technologyCost)+Number(data.otherCost);
        }

        var xmcb ="";//销售项目成本
        var xmlrsx = "";//项目利润速算
        if(gd!="" && fd!=""){
          xmcb =gd+fd;
          xmlrsx = data.money-xmcb;
        }

        this.form.setFieldsValue({
          fixedCSummary:gd,
          floatCSummary:fd,
          salesCost:xmcb,
          profitVelocity:xmlrsx
        });
      } else {
        this.spinning = false;
      }
    },
     handleSubmit(e) {
      e.preventDefault();
      const that = this;
      this.form.validateFields((err, values) => {
        if (!err) {
          this.save(0,values,that);
        } else {
          that.spinning = false;
        }
      });
    },
    saveData() {
      const that = this;
       this.form.validateFields((err, values) => {
        if (!err) {
         this.$confirm({
        title: "提示",
        content: "只能提交一次，提交后不能再编辑 ?",
        onOk() {
           that.save(1,values,that);
        },
         onCancel() {}
      });
        } else {
          that.spinning = false;
        }
      });
      
    },
    save(t,values,that){
      values.submitNot = Number(t);
      saveService(savePath, values)
        .then((res) => {
          that.$notification.success({
            message: "保存成功",
            description: "",
          });
          that.$emit("changeForm", {
            name: "base",
            data: res.result[0],
          });
          that.data = res.result[1];
          this.form.setFieldsValue({
            id:that.data.id
          });
          that.spinning = false;
        })
      .catch(() => {
        that.spinning = false;
      });
    },
    back() {
      back_to_list(this);
    }
  }
};
</script>
