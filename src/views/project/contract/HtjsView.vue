<template>
  <a-spin :spinning="spinning" style="margin-top:15px;margin-left:15px">
    <a-row class="form-row" :gutter="16">
      <a-col>
        <a-descriptions bordered :column="2">
          <a-descriptions-item label="销售合同号">{{data.coding}}</a-descriptions-item>
          <a-descriptions-item label="项目名称">{{data.projectName}}</a-descriptions-item>
          <a-descriptions-item label="来源">{{data.source_name}}</a-descriptions-item>
          <a-descriptions-item label="合同金额">{{data.money}}</a-descriptions-item>
          <a-descriptions-item label="销售项目成本">{{data.salesCost}}</a-descriptions-item>
          <a-descriptions-item label="项目利润速算">{{data.profitVelocity}}</a-descriptions-item>
          <a-descriptions-item label="固定成本汇总">{{data.fixedCSummary}}</a-descriptions-item>
          <a-descriptions-item label="货物成本">{{data.goodsCost}}</a-descriptions-item>
          <a-descriptions-item label="合同税务成本">{{data.contractTaxCost}}</a-descriptions-item>
          <a-descriptions-item label="浮动成本汇总">{{data.floatCSummary}}</a-descriptions-item>
          <a-descriptions-item label="差旅招待成本">{{data.travelCost}}</a-descriptions-item>
          <a-descriptions-item label="货物运输成本">{{data.transportCost}}</a-descriptions-item>
          <a-descriptions-item label="技术支持成本">{{data.technologyCost}}</a-descriptions-item>
          <a-descriptions-item label="其他成本">{{data.otherCost}}</a-descriptions-item>
          <a-descriptions-item label="是否提交">{{data.submitNot}}</a-descriptions-item>
        </a-descriptions>
      </a-col>
    </a-row>
  </a-spin>
</template>
<script>
import { queryService,back_to_list} from "@/api/manage";
import { MODEL_SYSTEM } from "@/store/mutation-types";

const findPath = "/" + MODEL_SYSTEM + "/cstatement/find";


export default {
  name: "Schedule_Form",
  props: ["cid", "type","base"],
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
     queryService(findPath, { cid: this.cid }).then(res => {
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
        this.data = res.result;

        // this.form.setFieldsValue(this.data);
      });
    this.spinning = false;
  },
  methods: {
    back() {
      back_to_list(this);
    }
  }
};
</script>
