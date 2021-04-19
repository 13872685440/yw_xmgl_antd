<template>
  <a-spin :spinning="spinning">
    <a-button
      v-if="!isview"
      type="primary"
      icon="plus"
      @click="$refs.createPosts.add('')"
      v-action:SYS_ADMINISTRATOR,Personnel_Administrator
      >新建</a-button
    >
    <a-table
      :columns="columns"
      :dataSource="data"
      :pagination="false"
      :loading="spinning"
      ref="table"
      ><span slot="action1" slot-scope="text, record">
        <template v-if="record.property === 'PRO001'"> 司内 </template>
        <template v-else>司外</template>
      </span>
      <span
        v-if="!isview"
        slot="action"
        slot-scope="text, record"
        v-action:SYS_ADMINISTRATOR,Personnel_Administrator
      >
        <template v-if="record.xh < 900">
          <a-divider type="vertical" />
          <a @click="$refs.createPosts.add(record.id)">
            <a-icon type="edit" style="color: #69aa46" />
          </a>
          <a-divider type="vertical" />
          <a @click="handleDelete(record.id)">
            <a-icon type="delete" style="color: #ff6347" />
          </a>
        </template>
      </span>
    </a-table>
    <create-posts ref="createPosts" :person_id="id" @ok="handleOk" />
  </a-spin>
</template>
<script>
import CreatePosts from "./modules/CreatePosts";
import { queryService, toService } from "@/api/manage";

export default {
  name: "Form_Posts",
  props: ["id", "isview"],
  components: {
    CreatePosts,
  },
  data() {
    return {
      spinning: true,
      columns: [
        {
          title: "类别",
          dataIndex: "action1",
          scopedSlots: { customRender: "action1" },
        },
        {
          title: "任职单位/部门",
          dataIndex: "organ_name",
        },
        {
          title: "岗位",
          dataIndex: "duty",
        },
        {
          title: "开始任职时间",
          dataIndex: "entrytime",
        },
        {
          title: "离任/离职时间",
          dataIndex: "leaftime",
        },
        {
          title: "操作",
          dataIndex: "action",
          width: "150px",
          scopedSlots: { customRender: "action" },
        },
      ],
      data: [],
      path: {},
    };
  },
  async created() {},
  mounted() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.query_path = prefix + "/postinformation/query_postinformation";
    this.path.delete_path = prefix + "/postinformation/delete_postinformation";

    this.renderTable();
    this.spinning = false;
  },
  methods: {
    handleOk() {
      this.renderTable();
    },
    async renderTable() {
      await queryService(this.path.query_path, {
        person_id: this.id,
      }).then((res) => {
        this.data = res.result;
      });
    },
    handleDelete(name) {
      this.spinning = true;
      const that = this;
      const parameter = { id: name };
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
    },
  },
};
</script>

<style lang="less" scoped>
.ant-table-wrapper {
  padding-top: 10px;
}
</style>
