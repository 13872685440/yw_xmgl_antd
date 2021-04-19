<template>
  <a-card :bordered="false">
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="48">
          <a-col :md="8" :sm="24">
            <a-form-item label="名称" :labelCol="{ offset: 1 }">
              <a-input v-model="queryParam.name" placeholder />
            </a-form-item>
          </a-col>
          <a-col :md="(!advanced && 8) || 24" :sm="24">
            <span
              class="table-page-search-submitButtons"
              :style="(advanced && { float: 'right', overflow: 'hidden' }) || {}"
            >
              <a-button type="primary" @click="$refs.table.refresh(true)">查询</a-button>
              <a-button
                style="margin-left: 8px"
                @click="
                  () => {
                    queryParam = {};
                    $refs.table.refresh(true);
                  }
                "
                >重置</a-button
              >
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <div class="table-operator">
      <a-button
        type="primary"
        icon="plus"
        v-action:SYS_ADMINISTRATOR,Personnel_Administrator
        @click="handleEdit('', 'add')"
        >新建</a-button
      >
    </div>
    <s-table
      ref="table"
      size="default"
      rowKey="code"
      :columns="columns"
      :data="loadData"
      :pageSize="20"
      ><span slot="org" slot-scope="text, record">
        <template v-for="(item,i) in record.org_names">
          <div :key="i">{{ item }}</div>
        </template> </span
      ><span slot="role" slot-scope="text, record">
        <template v-for="(item,i) in record.role_names">
          <div :key="i">{{ item }}</div>
        </template>
      </span>
      <span
        slot="action"
        slot-scope="text, record"
        v-action:SYS_ADMINISTRATOR,Personnel_Administrator
      >
        <template>
          <a @click="handleEdit(record.id, 'add')" title="编辑">
            <a-icon type="edit" style="color: #69aa46" />
          </a>
          <a-divider type="vertical" />
          <a @click="handleDelete(record.id)">
            <a-icon type="delete" style="color: #ff6347" title="删除" />
          </a>
        </template>
      </span>
    </s-table>
  </a-card>
</template>
<script>
import { STable } from "@/components";
import { queryService, handle_delete } from "@/api/manage";
import { queryList } from "@/utils/mixin";

export default {
  name: "TableList",
  components: { STable },
  mixins: [queryList],
  data() {
    return {
      // 表头
      columns: [
        {
          title: "岗位名",
          dataIndex: "name",
        },
        {
          title: "关联组织机构",
          dataIndex: "org",
          scopedSlots: { customRender: "org" },
        },
        {
          title: "关联角色",
          dataIndex: "role",
          scopedSlots: { customRender: "role" },
        },
        {
          title: "排序",
          dataIndex: "xh",
          sorter: true,
        },
        {
          title: "操作",
          dataIndex: "action",
          width: "150px",
          scopedSlots: { customRender: "action" },
        },
      ],
      // 加载数据方法 必须为 Promise 对象
      loadData: (parameter) => {
        parameter.params = this.queryParam;
        return queryService(this.path.query_path, parameter).then((res) => {
          return res.result;
        });
      },
    };
  },
  created: function () {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.query_path = prefix + this.$route.path;
    this.path.delete_path = prefix + "/post/delete";
  },
  methods: {
    handleDelete(id) {
      handle_delete(this, id, this.path.delete_path);
    },
  },
};
</script>
