<template>
  <a-card :bordered="false">
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="48">
          <a-col :md="8" :sm="24">
            <a-form-item label="公司名称">
              <a-input v-model="queryParam.corporatName" placeholder />
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="24">
            <a-form-item label="所属业务员">
              <a-input v-model="queryParam.salesman" placeholder />
            </a-form-item>
          </a-col>
           <a-col :md="8" :sm="24">
            <a-form-item label="法定代表人">
              <a-input v-model="queryParam.legalPerson" placeholder />
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="24">
            <a-form-item label="提交人">
              <a-input v-model="queryParam.userName" placeholder />
            </a-form-item>
          </a-col>
          <a-col :md="!advanced && 8 || 24" :sm="24">
            <span class="table-page-search-submitButtons" :style="advanced && { float: 'right', overflow: 'hidden' } || {} ">
              <a-button type="primary" @click="$refs.table.refresh(true)">查询</a-button>
              <a-button style="margin-left: 8px" @click="() => {queryParam = {};$refs.table.refresh(true)}" >重置</a-button>
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <div class="table-operator">
      <a-button type="primary" icon="plus" v-action:SYS_ADMINISTRATOR @click="handleEdit('','add')">新建</a-button>
      <excel-upload :upload_path="path.upload_path" v-action:SYS_ADMINISTRATOR>导入</excel-upload>
      <a @click="downloadExecl">点击此处下载模版</a>     
    </div>
    <s-table ref="table" size="default" :columns="columns" :data="loadData" :pageSize="20">
      <span slot="action" slot-scope="text, record">
        <template>
         <a @click="handleView(record.id, 'view')" title="查看" >
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
import ExcelUpload from "@/components/File/ExcelUpload";
import moment from "moment";

export default {
  name: "TableList",
  components: { STable,ExcelUpload},
  mixins: [queryList],
  data() {
    return {
      //上传文件请求头
      headers: {
        authorization: "authorization-text",
      },
      // 表头
      columns: [
        {
          title: "序号",
          dataIndex: "num",
          width:"60px",
        },
        {
          title: "公司名称",
          dataIndex: "corporatName",
          sorter: (a, b) => a.corporatName.length - b.corporatName.length ,
        },
        {
          title: "所属业务员",
          dataIndex: "salesman",
          sorter: (a, b) => a.salesman.length - b.salesman.length ,
        },
         {
          title: "地址",
          dataIndex: "addr",
          sorter: (a, b) => a.addr.length - b.addr.length ,
        },
        {
          title: "注册资金",
          dataIndex: "registerCapital",
          sorter: (a, b) => a.registerCapital - b.registerCapital ,
        },
         {
          title: "成立日期",
          dataIndex: "establishDate",
          customRender: function(val) {
            return val ? moment(val).format('YYYY-MM-DD') : ''
          },
          sorter: (a, b) => a.establishDate - b.establishDate ,
        },      
        {
          title: "法定代表人",
          dataIndex: "legalPerson",
          sorter: (a, b) => a.legalPerson - b.legalPerson ,
        },  
        {
          title: "提交人",
          dataIndex: "userName",
          sorter: (a, b) => a.userName.length - b.userName.length ,
        },
        {
          title: "提交时间",
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
            });
          return res.result;
        });
      }
    };
  },
  created() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.query_path = prefix + this.$route.path;
    this.path.delete_path = prefix + "/customer/revoke";
    this.path.upload_path = "/api" +prefix + "/customer/improtExcel";
  },
  methods: {
    handleDelete(id) {
      handle_delete(this, id, this.path.delete_path);
    },
     downloadExecl() {
     
    },

  }
};
</script>