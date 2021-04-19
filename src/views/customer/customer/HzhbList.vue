<template>
  <a-spin :spinning="spinning">
    <a-button v-if="!isview" type="primary" icon="plus" v-action:SYS_ADMINISTRATOR   @click="$refs.createHzhb.add('')">新建</a-button>
    <a-table :columns="columns" :dataSource="data" :pagination="false" :loading="spinning" ref="table">
      <span slot="action" slot-scope="text, record">
        <template v-if="!isview">
          <a-divider type="vertical" />
          <a  @click="$refs.createHzhb.add(record.id)" title="编辑">
            <a-icon type="edit" style="color:#69aa46" />
          </a>
          <a-divider type="vertical" />
          <a @click="handleDelete(record.id)" title="删除">
            <a-icon type="delete" style="color:#FF6347" />
          </a>
        </template>
      </span>
    </a-table>
    <create-hzhb ref="createHzhb" :pid="pid"  @ok="handleOk" />
  </a-spin>
</template>
<script>
import createHzhb from "./modules/CreateHzhb";
import { queryService, toService } from "@/api/manage";
import moment from "moment";

export default {
  name: "Form_Partners",
  props: ["pid", "isview"],
  components: {
    createHzhb,
  },
  data() {
    return {
      spinning: true,
      columns: [
        {
         title: '序号',
          dataIndex: 'num',
          scopedSlots: { customRender: "action1" },
        },{
          title: '公司名称',
          dataIndex: 'corporatName',
        },{
          title: '合作规模',
          dataIndex: 'cooperationScale',
        },{
          title: '合作方式',
          dataIndex: 'cooperationMode',
        },{
          title: '评估',
          dataIndex: 'assess',
        },{
          title: '提交时间',
          dataIndex: 'ct',
          customRender: function(val) {
            return val ? moment(val).format('YYYY-MM-DD') : ''
          },
        },{
          title: "操作",
          dataIndex: "action",
          width: "120px",
          scopedSlots: { customRender: "action" }
        }
      ],
      data: [],
      path: {},
    };
  },
  async created() {},
  mounted() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.query_path = prefix + "/partners/getList";
    this.path.find_path = prefix + "/customer/find";
    this.path.delete_path = prefix + "/partners/delete";


    this.renderTable();
    this.spinning = false;
  },
  methods: {
    handleOk() {
      this.renderTable();
    },
    async renderTable() {
       await queryService(this.path.find_path, { id: this.pid }).then(res => {
        this.$emit("changeForm", {
          name: "base",
          data: res.result,
        });
      });
      await queryService(this.path.query_path, {
        customer_id: this.pid,
      }).then((res) => {
        res.result.forEach((value,index) => {
          value.num = index+1;
        });
        this.data = res.result;
      });
    },
     handleDelete(id) {
      this.spinning = true;
      const that = this;
      const parameter = { id: id };
      this.$confirm({
        title: "提示",
        content: "真的要删除该记录吗 ?",
        onOk() {
          toService("post", that.path.delete_path, parameter).then((res) => {
            if (res.code === "200") {
              that.$notification.success({
                message: "删除成功",
                description: "",
              });
              that.renderTable();
            } else {
              that.$notification.error({
                message: "删除失败",
                description: res.result,
              });
            }
          });
        },
        onCancel() {},
      });
      this.spinning = false;
    }
  },
};
</script>

<style lang="less" scoped>
.ant-table-wrapper {
  padding-top: 10px;
}
</style>
