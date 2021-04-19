<template>
  <a-spin :spinning="spinning" style="margin-top:15px;margin-left:15px">
    <a-row class="form-row" :gutter="16">
      <a-col :xs="{ span: 24, offset: 1 }" :lg="{ span: 15, offset: 5 }" >
        <a-descriptions bordered :column="1">
          <a-descriptions-item label="标题">{{data.title}}</a-descriptions-item>
          <a-descriptions-item label="提交人">{{data.userName}}</a-descriptions-item>
          <a-descriptions-item label="提交时间">{{data.ct}}</a-descriptions-item>
          <a-descriptions-item label="内容">{{data.content}}</a-descriptions-item>
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
import { queryService, back_to_list} from "@/api/manage";

export default {
  name: "Commoncolumn_Form",
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
    this.path.edit_path = prefix + "/commoncolumn/edit";
  },
  mounted() {
    queryService(this.path.edit_path, { id: this.formId }).then((res) => {
       this.data = res.result;
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