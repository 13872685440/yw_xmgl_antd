<template>
  <a-spin :spinning="spinning" style="margin-top:15px;margin-left:15px">
    <a-row class="form-row" :gutter="16">
      <a-col>
        <a-descriptions bordered :column="2">
          <a-descriptions-item label="跟单号">{{data.coding}}</a-descriptions-item>
          <a-descriptions-item label="客户公司名称">{{data.companyName}}</a-descriptions-item>
          <a-descriptions-item label="关联销售">{{data.xshtCodding}}</a-descriptions-item>
          <a-descriptions-item label="项目名称">{{data.projectName}}</a-descriptions-item>
          <a-descriptions-item label="项目来源">{{data.source_name}}</a-descriptions-item>
          <a-descriptions-item label="项目负责人">{{data.directorName}}</a-descriptions-item>
          <a-descriptions-item label="负责人电话">{{data.directorPhone}}</a-descriptions-item>
          <a-descriptions-item label="提交人">{{data.userName}}</a-descriptions-item>
          <a-descriptions-item label="提交时间">{{data.ct}}</a-descriptions-item>
          <a-descriptions-item label="跟单记录状态"> 
            <div :style="productState(data.gdjlState_code)">{{data.gdjlState_name}}</div>
          </a-descriptions-item>
          <a-descriptions-item label="售前支持与设计报价状态">
            <div :style="productState(data.zsbjState_code)">{{data.zsbjState_name}}</div>
          </a-descriptions-item>
          <a-descriptions-item label="成败总结状态">
            <div :style="productState(data.cbzjState_code)">{{data.cbzjState_name}}</div>
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
