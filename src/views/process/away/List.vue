<template>
  <a-card :bordered="false">
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="48">
          <a-col :md="8" :sm="24">
            <a-form-item label="出差发起人">
              <a-input v-model="queryParam.userName" placeholder />
            </a-form-item>
          </a-col>
          <!-- <a-col :md="8" :sm="24">
            <a-form-item label="审核人">
              <a-input v-model="queryParam.userName" placeholder />
            </a-form-item>
          </a-col> -->
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
        },
        {
          title: "出差发起人",
          dataIndex: "userName",
          sorter: (a, b) => a.userName.length - b.userName.length ,
        },
        {
          title: "同行人",
          dataIndex: "txry",
        },
        {
          title: "出差计划与目的",
          dataIndex: "plan",
          sorter: (a, b) => a.plan.length - b.plan.length ,
        },
        {
          title: "行程安排",
          dataIndex: "scheduling",
          sorter: (a, b) => a.scheduling.length - b.scheduling.length ,
        },
        {
          title: "审核状态",
          // dataIndex: "userName",
          // sorter: (a, b) => a.userName.length - b.userName.length ,
        },
        {
          title: "审核人",
          // dataIndex: "userName",
          // sorter: (a, b) => a.userName.length - b.userName.length ,
        },
         {
          title: "出差成果反馈",
          dataIndex: "feedback",
          sorter: (a, b) => a.feedback.length - b.feedback.length ,
        },
         {
          title: "备注",
          dataIndex: "remark",
          sorter: (a, b) => a.remark.length - b.remark.length ,
        },
        {
          title: "发起时间",
          dataIndex: "ct",
          customRender: function(val) {
            return val ? moment(val).format('YYYY-MM-DD') : ''
          },
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
              value.txry = null;
              value.colleagues_names.forEach((value1) => {
                value.txry = value.txry!=null?value.txry+"、"+ value1:value1;
               });
            });
          return res.result;
        });
      }
    };
  },
  created() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.query_path = prefix + this.$route.path;
    this.path.delete_path = prefix + "/away/delete";
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