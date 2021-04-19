<template>
  <a-spin :spinning="spinning" style="margin-top:15px;margin-left:15px">
    <a-row class="form-row" :gutter="16">
      <a-col>
        <a-descriptions bordered :column="2">
          <a-descriptions-item label="公司名称">{{data.corporatName}}</a-descriptions-item>
          <a-descriptions-item label="所属业务员">{{data.salesman}}</a-descriptions-item>
          <a-descriptions-item label="地址">{{data.addr}}</a-descriptions-item>
          <a-descriptions-item label="注册资金">{{data.registerCapital}}</a-descriptions-item>
          <a-descriptions-item label="成立日期">{{data.establishDate}}</a-descriptions-item>
          <a-descriptions-item label="法定代表人">{{data.legalPerson}}</a-descriptions-item>
          <a-descriptions-item label="企业网站">{{data.enterpriseWebsite}}</a-descriptions-item>
          <a-descriptions-item label="企业规模（人数）">{{data.enterpriseScale}}</a-descriptions-item>
          <a-descriptions-item label="所属行业">{{data.industry}}</a-descriptions-item>
          <a-descriptions-item label="在所属行业位置">{{data.industryPosition}}</a-descriptions-item>
          <a-descriptions-item label="客户分级">{{data.customerRating}}</a-descriptions-item>
          <a-descriptions-item label="主要业务方向" :span="2">{{data.businessDirection}}</a-descriptions-item>
          <a-descriptions-item label="相关成功案例"  :span="2">{{data.successCase}}</a-descriptions-item>
          <a-descriptions-item label="与我司相关业务范畴"  :span="2">{{data.businessScope}}</a-descriptions-item>
          <a-descriptions-item label="客户年AV采购额预估">{{data.avEstimate}}</a-descriptions-item>
          <a-descriptions-item label="客户从我司采购额预估">{{data.myEstimate}}</a-descriptions-item>
          <a-descriptions-item label="近期合作目标"  :span="2">{{data.nearCooTtarget}}</a-descriptions-item>
          <a-descriptions-item label="远期合作目标"  :span="2">{{data.farCooTtarget}}</a-descriptions-item>
          <a-descriptions-item label="公司组织架构图"  :span="2">{{data.framework}}</a-descriptions-item>
          <a-descriptions-item label="备注"  :span="2">{{data.remark}}</a-descriptions-item>
          <a-descriptions-item label="提交人">{{data.userName}}</a-descriptions-item>
          <a-descriptions-item label="提交时间">{{data.ct}}</a-descriptions-item>
        </a-descriptions>
      </a-col>
    </a-row>
  </a-spin>
</template>
<script>
import { back_to_list} from "@/api/manage";

export default {
  name: "Aftersales_Form",
  props: ["formId", "type","base","XMSTATE"],
  data() {
    return {
      form: this.$form.createForm(this), 
      spinning: true,
      memberLoading: false,
      path: {},
      data:{},
      formItemLayout: {
        labelCol: { span: 3 },
      },
    };
  },
  mounted() {
    this.data = this.base;
    this.spinning = false;
  },
  methods: {
    productState (stateCode) {
      var color = "";
      this.XMSTATE.forEach((item) => {
        if(stateCode==item.code){
         color = "color:"+item.colorCode;
        }
      });
      return color;
    },
    back() {
      back_to_list(this);
    }
  }
};
</script>
