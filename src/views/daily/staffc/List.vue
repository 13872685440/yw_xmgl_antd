<template>
  <a-card :bordered="false">
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="48">
          <a-col :md="8" :sm="24">
            <a-form-item label="姓名">
              <a-input v-model="queryParam.name" placeholder />
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="24">
            <a-form-item label="办公电话">
              <a-input v-model="queryParam.worktel" placeholder />
            </a-form-item>
          </a-col>
          <a-col :md="!advanced && 8 || 24" :sm="24">
            <span
              class="table-page-search-submitButtons"
              :style="advanced && { float: 'right', overflow: 'hidden' } || {} ">
              <a-button type="primary" @click="$refs.table.refresh(true)">查询</a-button>
              <a-button
                style="margin-left: 8px"
                @click="() => {queryParam = {};$refs.table.refresh(true)}"
              >重置</a-button>
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
        @click="handleEdit('','add')"
      >新建</a-button>
    </div>
    <s-table ref="table" size="default" :columns="columns" :data="loadData" :pageSize="20">
      <span slot="action" slot-scope="text, record">
        <template>
          <a @click="handleView(record.id, 'view')" title="查看">
            <a-icon type="eye" style="color: blue" />
          </a>
          <a-divider type="vertical" />
          <a @click="handleEdit(record.id,'add')" title="编辑">
            <a-icon type="edit" style="color:#69aa46" />
          </a>
          <a-divider type="vertical" />
          <a @click="handleDelete(record.id)" title="删除">
            <a-icon type="delete" style="color:#FF6347" />
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
import moment from "moment";


export default {
  name: "TableList",
  props: ["searchCode"],
  components: { STable },
  mixins: [queryList],
  data() {
    return {
      // 表头
      columns: [
        {
          title: "序号",
          dataIndex: "num",
          width:"60px"
        },
        {
          title: "姓名",
          dataIndex: "name",
          sorter: (a, b) => a.name.length - b.name.length ,
        },
        {
          title: "办公电话",
          dataIndex: "worktel",
          sorter: (a, b) => a.worktel - b.worktel ,
        },
        {
          title: "个人电话",
          dataIndex: "phone",
          sorter: (a, b) => a.phone - b.phone ,
        },
        {
          title: "电子邮件",
          dataIndex: "email",
          sorter: (a, b) => a.email - b.email ,
        },
        {
          title: "地址",
          dataIndex: "address",
          sorter: (a, b) => a.address.length - b.address.length ,
          ellipsis: true,
        },
        {
          title: "备注",
          dataIndex: "remark",
          ellipsis: true,
        },
        {
          title: "提交人",
          dataIndex: "userName",
          width:"100px",
          sorter: (a, b) => a.userName.length - b.userName.length ,
        },
        {
          title: "提交时间",
          dataIndex: "ct",
          customRender: function(val) {
            return val ? moment(val).format('YYYY-MM-DD') : ''
          },
           width:"110px",
          sorter: (a, b) => a.ct - b.ct ,
        },
        {
          title: "操作",
          dataIndex: "action",
          width: "120px",
          scopedSlots: { customRender: "action" }
        }
      ],
      // 加载数据方法 必须为 Promise 对象
      loadData: parameter => {
        parameter.params = this.queryParam;
        return queryService(this.path.query_path, parameter).then(res => {
          let result = res.result
            const filterList = result.data.filter(_ => _.account !== 'admin')
            res.result.data = filterList
            res.result.data.forEach((value,index) => {
              value.num = (result.pageNo-1)*result.pageSize+index+1;
            });
          return res.result;
        });
      }
    };
  },
  created() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.query_path = prefix + this.$route.path;
    this.path.delete_path = prefix + "/staffc/delete";
  },
  methods: {
     handle_lower(id, state) {
      this.handle_edit_type(this.code, state, "add");
    },
    handleDelete(id) {
      handle_delete(this, id, this.path.delete_path);
    }
  }
};
</script>