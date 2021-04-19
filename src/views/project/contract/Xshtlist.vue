<template>
  <a-spin :spinning="spinning">
    <a-button v-if="!isview && state!='00100002'" type="primary" icon="plus" v-action:SYS_ADMINISTRATOR   @click="$refs.createContent.add('')">新建</a-button>
    <a-table :columns="columns" :dataSource="data" :pagination="false" :loading="spinning" ref="table"></a-table>
    <create-content ref="createContent" :keyValue="id" type="4" @ok="handleOk"/>
  </a-spin>
</template>
<script>
import createContent from "./modules/CreateContent";
import { queryService } from "@/api/manage";
import moment from "moment";

export default {
  name: "Form_Content",
  props: ["id", "isview","state"],
  components: {
    createContent,
  },
  data() {
    return {
      spinning: true,
      columns: [
        {
         title: '序号',
          dataIndex: 'num',
        },
         {
          title: '提交人',
          dataIndex: 'userName',
        },
        {
          title: '提交时间',
          dataIndex: 'ct',
        },
        {
          title: '内容',
          dataIndex: 'content',
        },
        {
          title: '状态',
          dataIndex: 'state_name',
        },
      ],
      data: [],
      path: {},
    };
  },
  async created() {},
  mounted() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.query_path = prefix + "/content/getList";
    this.path.find_path = prefix + "/contract/find";

    this.renderTable();
    this.spinning = false;
  },
  methods: {
    handleOk() {
      this.renderTable();
    },
    async renderTable() {
      await queryService(this.path.find_path, { id: this.id }).then(res => {
        this.$emit("changeForm", {
          name: "base",
          data: res.result,
        });
      });
      await queryService(this.path.query_path, {
        keyValue: this.id,
        type:"4",
      }).then((res) => {
         res.result.forEach((value,index) => {
           value.ct = moment(res.result.ct).format("YYYY-MM-DD")
            value.num = index+1;
          });
        this.data = res.result;
      });
    },
  },
};
</script>

<style lang="less" scoped>
.ant-table-wrapper {
  padding-top: 10px;
}
</style>
