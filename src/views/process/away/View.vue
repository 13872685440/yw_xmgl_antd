<template>
  <a-spin :spinning="spinning" style="margin-top:15px;margin-left:15px" >
    <a-row class="form-row" :gutter="10" >
      <a-col :xs="{ span: 24, offset: 1 }" :lg="{ span: 15, offset: 5 }" >
        <a-descriptions bordered :column="1" >
          <a-descriptions-item label="出差发起人">{{data.userName}}</a-descriptions-item>
          <a-descriptions-item label="出差发起时间">{{data.ct}}</a-descriptions-item>
          <a-descriptions-item label="同行人">{{data.txry}}</a-descriptions-item>
          <a-descriptions-item label="出差计划与目的">{{data.plan}}</a-descriptions-item>
          <a-descriptions-item label="行程安排">{{data.scheduling}}</a-descriptions-item>
          <a-descriptions-item label="审核状态"></a-descriptions-item>
          <a-descriptions-item label="审核人"></a-descriptions-item>
          <a-descriptions-item label="出差成果反馈">{{data.feedback}}</a-descriptions-item>
          <a-descriptions-item label="备注">{{data.remark}}</a-descriptions-item>

        </a-descriptions>
      </a-col>
      <a-row class="form-row" :gutter="16" style="text-align: center;">
        <a-col :xl="{span: 23, offset: 1}" :lg="{span: 24}" :md="{span: 24}" :sm="24" >
          <a-button style="margin-top:15px" @click="back">返回</a-button>
        </a-col>
      </a-row>
    </a-row>
   
  </a-spin>
</template>

<script>
import { queryService, back_to_list } from "@/api/manage";

export default {
  name: "Away_Form",
  props: ["formId","type",],
  data() {
    return {
      form: this.$form.createForm(this),
      spinning: true,
      path: {},
      data:{},
      errordata:{},
      
    };
  },
  created() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.edit_path = prefix + "/away/edit";
  },
  mounted() {
    queryService(this.path.edit_path, { id: this.formId }).then((res) => {
      let txry = null;
       res.result.colleagues_names.forEach((value,index1) => {
        txry = txry!=null?txry+"、"+ value:value;
        });
        res.result.txry = txry;
       this.data =res.result;
    });
    this.spinning = false;
  },
  methods: {
    back() {
      back_to_list(this);
    },
  },
};
</script>
<style>
.ant-descriptions-bordered .ant-descriptions-item-label {
    width: 150px;
}
</style>