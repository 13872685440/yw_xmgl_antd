<template>
  <a-spin :spinning="spinning" style="margin-top:15px;margin-left:15px">
    <a-row class="form-row" :gutter="16">
      <a-col>
        <a-descriptions bordered :column="2">
          <a-descriptions-item label="售后单号">{{data.coding}}</a-descriptions-item>
          <a-descriptions-item label="关联销售合同号">{{data.xsht_coding}}</a-descriptions-item>
          <a-descriptions-item label="对应项目">{{data.projectName}}</a-descriptions-item>
          <a-descriptions-item label="描述">{{data.describe}}</a-descriptions-item>
          <a-descriptions-item label="性质">{{data.nature_name}}</a-descriptions-item>
          <a-descriptions-item label="提交人">{{data.userName}}</a-descriptions-item>
          <a-descriptions-item label="提交时间">{{data.ct}}</a-descriptions-item>
          <a-descriptions-item label="售后往来状态"> 
            <div :style="productState(data.shlwState_code)">{{data.shlwState_name}}</div>
          </a-descriptions-item>
          <a-descriptions-item label="收款与开票状态">
            <div :style="productState(data.skykpState_code)">{{data.skykpState_name}}</div>
          </a-descriptions-item>
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
