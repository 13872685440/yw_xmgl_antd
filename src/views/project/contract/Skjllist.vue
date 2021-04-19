<template>
  <a-spin :spinning="spinning">
    <a-card  class="title-norm">
      销售合同金额：<span>{{base.money}}</span>合同收款次数与金额：<span>{{base.norm}}</span>
    </a-card>
    <a-button v-if="state!='00100002'" type="primary" icon="plus" v-action:SYS_ADMINISTRATOR   @click="$refs.createSk.add('')">新建</a-button>
    <a-table :columns="columns" :dataSource="data" :pagination="false" :loading="spinning" ref="table"></a-table>
    <create-sk ref="createSk" :keyValue="id"  type="2" @ok="handleOk"/>
  </a-spin>
</template>
<script>

import createSk from "./modules/CreateSk";
import { queryService } from "@/api/manage";
import moment from "moment";

export default {
  name: "Form_Sk",
  props: ["id", "base","state"],
  components: {
    createSk,
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
          title: '开票金额',
          dataIndex: 'price',
        },
         {
          title: '开票比例',
          dataIndex: 'ratio',
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
          title: '备注',
          dataIndex: 'content',
        },{
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
    this.path.query_path = prefix + "/offer/getList";
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
        type:"2",
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

  .title-norm{
    margin-bottom:10px;height:60px
  }

  .title-norm span{
    color:red;
    margin-right: 20px;
    font-weight: 500;
  }
</style>
