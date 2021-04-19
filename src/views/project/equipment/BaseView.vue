<template>
  <a-spin :spinning="spinning" style="margin-top:15px;margin-left:15px">
    <a-row class="form-row" :gutter="16">
      <a-col>
        <a-descriptions bordered :column="2">
          <a-descriptions-item label="采购合同号">{{data.coding}}</a-descriptions-item>
          <a-descriptions-item label="关联销售合同号">{{data.xsht_coding}}</a-descriptions-item>
          <a-descriptions-item label="对应项目">{{data.projectName}}</a-descriptions-item>
          <a-descriptions-item label="供应商名称">{{data.supplierName}}</a-descriptions-item>
          <a-descriptions-item label="采购合同金额">{{data.money}}</a-descriptions-item>
          <a-descriptions-item label="合同质保期">{{data.warranty}}</a-descriptions-item>
          <a-descriptions-item label="最后一次收款时间">{{data.lastfkdate}}</a-descriptions-item>
          <a-descriptions-item label="提交人">{{data.userName}}</a-descriptions-item>
          <a-descriptions-item label="提交时间">{{data.ct}}</a-descriptions-item>
          <a-descriptions-item label="采购合同扫描状态"> 
            <div :style="productState(data.cghtsmState_code)">{{data.cghtsmState_name}}</div>
          </a-descriptions-item>
          <a-descriptions-item label="付款与收票状态">
            <div :style="productState(data.fkyspState_code)">{{data.fkyspState_name}}</div>
          </a-descriptions-item>
          <a-descriptions-item label="收货状态">
            <div :style="productState(data.shState_code)">{{data.shState_name}}</div>
          </a-descriptions-item>
          <a-descriptions-item label="退换货状态">
            <div :style="productState(data.thhState_code)">{{data.thhState_name}}</div>
          </a-descriptions-item>
        </a-descriptions>
      </a-col>
    </a-row>
  </a-spin>
</template>
<script>
import { back_to_list} from "@/api/manage";

export default {
  name: "Schedule_Form",
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
