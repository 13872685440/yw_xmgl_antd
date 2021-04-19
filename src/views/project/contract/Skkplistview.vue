<template>

  <a-spin :spinning="spinning">
    
    <div id="components-table-demo-size">
    <a-card class="title-norm">
      销售合同金额：<span>{{base.money}}</span>合同收款次数与金额：<span>{{base.norm}}</span>
    </a-card>
     <a-card title="开票记录" class="title-table">
      <!-- <h4>开票记录</h4> -->
      <a-table :columns="columns" :dataSource="kpdata" :pagination="false" :loading="spinning" ref="table" size="small"></a-table>
      </a-card>
       <a-card title="收款记录" class="title-table">
      <!-- <h4>收款记录</h4> -->
      <a-table :columns="columns" :dataSource="skdata" :pagination="false" :loading="spinning" ref="table" size="small"></a-table>
      </a-card>
    </div>
  </a-spin>
</template>
<script>

import { queryService } from "@/api/manage";
import moment from "moment";

export default {
  name: "Form_Offer",
  props: ["id", "base"],
  
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
      kpdata: [],
      skdata: [],
      path: {},
    };
  },
  mounted() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.query_path = prefix + "/offer/getList";
    this.path.find_path = prefix + "/contract/find";
    this.renderTable();
    this.spinning = false;

  },
  methods: {
    async renderTable() {
      await queryService(this.path.query_path, {
        keyValue: this.id,
        type:"1",
      }).then((res) => {
         res.result.forEach((value,index) => {
           value.ct = moment(res.result.ct).format("YYYY-MM-DD")
            value.num = index+1;
          });
        this.kpdata = res.result;
      });
      await queryService(this.path.query_path, {
        keyValue: this.id,
        type:"2",
      }).then((res) => {
         res.result.forEach((value,index) => {
           value.ct = moment(res.result.ct).format("YYYY-MM-DD")
            value.num = index+1;
          });
        this.skdata = res.result;
      });
    },
  },
};
</script>

<style lang="less" scoped>
.ant-table-wrapper {
  padding-top: 10px;
}

.title-table {
  margin-bottom: 16px;
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
