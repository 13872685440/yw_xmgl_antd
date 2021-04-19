<template>
  <a-card :bordered="false">
    <a-row :gutter="8">
      <a-col :span="5">
        <s-tree
          :dataSource="tree"
          :search="true"
          :expandedKeys.sync="expandedKeys"
          :selectedKeys.sync="selectedKeys"
          :autoExpandParent="autoExpandParent"
          :defaultExpandedKeys="defaultExpandedKeys"
          :searchValue="searchValue"
          @expand="onExpand"
          @select="select"
          @change="onChange"
          ref="tree"
        ></s-tree>
      </a-col>
      <a-col :span="19">
        <div class="table-page-search-wrapper">
          <a-form layout="inline">
            <a-row :gutter="48">
              <a-col :md="8" :sm="24">
                <a-form-item label="姓名">
                  <a-input v-model="queryParam.name" placeholder />
                </a-form-item>
              </a-col>
              <a-col :md="(!advanced && 8) || 24" :sm="24">
                <span
                  class="table-page-search-submitButtons"
                  :style="(advanced && { float: 'right', overflow: 'hidden' }) || {}"
                >
                  <a-button type="primary" @click="$refs.table.refresh(true)"
                    >查询</a-button
                  >
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
            v-action:SYS_ADMINISTRATOR
            @click="handle_lower('', 'add')"
            >新建</a-button
          >
          <excel-upload :upload_path="path.upload_path" v-action:SYS_ADMINISTRATOR
            >导入</excel-upload
          >
        </div>
        <s-table
          ref="table"
          size="default"
          :columns="columns"
          :data="loadData"
          :pageSize="20"
        >
          <span slot="action" slot-scope="text, record">
            <template
              ><a @click="handleView(record.id, 'view')" title="查看">
                <a-icon type="eye" style="color: blue" />
              </a>
              <span v-action:SYS_ADMINISTRATOR,Personnel_Administrator
                ><a-divider type="vertical" />
                <a @click="handleEdit(record.id, 'add')" title="编辑">
                  <a-icon type="edit" style="color: #69aa46" />
                </a>
                <a-divider type="vertical" />
                <a @click="handleDelete(record.person_id)" title="删除">
                  <a-icon type="delete" style="color: #ff6347" /> </a
              ></span>
            </template>
          </span>
        </s-table>
      </a-col>
    </a-row>
  </a-card>
</template>
<script>
import STree from "@/components/Tree/STree";
import { STable } from "@/components";
import { queryService, handle_delete } from "@/api/manage";
import { queryList, treeList, generateList } from "@/utils/mixin";
import ExcelUpload from "@/components/File/ExcelUpload";

export default {
  name: "TreeList",
  props: ["searchCode"],
  components: {
    STable,
    STree,
    ExcelUpload,
  },
  mixins: [queryList, treeList],
  data() {
    return {
      // 表头
      columns: [
        {
          title: "员工编号",
          dataIndex: "loginName",
        },
        {
          title: "姓名",
          dataIndex: "person_name",
        },
        {
          title: "所在部门",
          dataIndex: "organ_name",
        },
        {
          title: "联系电话",
          dataIndex: "phone",
        },
        {
          title: "任职状态",
          dataIndex: "leaf_data",
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
        this.handleIniTree();
        this.queryParam.code = this.code;
        parameter.params = this.queryParam;
        return queryService(this.path.query_path, parameter).then((res) => {
          return res.result;
        });
      },
      code: "",
      path: {},
    };
  },
  created() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.query_path = prefix + this.$route.path;
    this.path.delete_path = prefix + "/postinformation/delete";
    this.path.tree_path = prefix + "/organization/tree";
    this.path.upload_path = "/api" + prefix + "/postinformation/improt";
  },
  methods: {
    handle_lower(id, state) {
      this.handle_edit_type(this.code, state, "add");
    },
    handleDelete(id) {
      handle_delete(this, id, this.path.delete_path);
    },
    handleIniTree() {
      if (this.code === "") {
        this.code = this.searchCode;
      }
      queryService(this.path.tree_path, { other: 1 }).then((res) => {
        this.tree = res.result;
        this.expandedKeys.push(this.code);
        this.selectedKeys.push(this.code);
        generateList(res.result, this.dataList);
      });
    },
    select(key) {
      this.$emit("search", key[0]);
      this.code = key[0];
      this.selectedKeys = key;
      this.queryParam = { code: key[0] };
      this.$refs.table.refresh(true);
    },
  },
};
</script>
<style>
.ant-tree-node-content-wrapper {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  max-width: 80%;
}
</style>
