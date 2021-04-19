<template>
  <a-card :bordered="false">
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="48">
          <a-col :md="8" :sm="24">
            <a-form-item label="销售合同号">
              <a-input v-model="queryParam.coding" placeholder />
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="24">
            <a-form-item label="合同甲方全称">
              <a-input v-model="queryParam.partyaName" placeholder />
            </a-form-item>
          </a-col><a-col :md="8" :sm="24">
            <a-form-item label="项目名称">
              <a-input v-model="queryParam.projectName" placeholder />
            </a-form-item>
          </a-col><a-col :md="8" :sm="24">
            <a-form-item label="关联跟单">
              <a-input v-model="queryParam.xmgd_coding" placeholder />
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="24">
            <a-form-item label="提交人">
              <a-input v-model="queryParam.userName" placeholder />
            </a-form-item>
          </a-col>
           <a-col :xl="{span: 7, offset: 1}" :lg="{span: 8}" :md="{span: 12}" :sm="24">
            <a-form-item label="项目来源">
              <a-select placeholder="请选择项目来源" v-model="queryParam.source_code">
              <a-select-option v-for="source in SOURCE" :key="source.code" :value="source.code">{{ source.name }}</a-select-option>
            </a-select>
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
      <a-button
        type="primary"
        icon="plus"
        v-action:SYS_ADMINISTRATOR
        @click="handleEdit('','add')"
      >新建</a-button>
    </div>
    <s-table ref="table" size="default" :columns="columns" :data="loadData" :pageSize="20" >
      <span slot="state" slot-scope="text, record">
        <template>
         <a title="销售合同扫描状态">
            <a-badge class="login-form-forgot" :style="productState(record.htsmState_code)"/>
          </a>
          <a title="开票状态">
            <a-badge class="login-form-forgot" :style="productState(record.kpState_code)"/>
          </a>
          <a title="收款状态">
            <a-badge class="login-form-forgot" :style="productState(record.skState_code)"/>
          </a>
          <a title="发货状态">
            <a-badge class="login-form-forgot" :style="productState(record.fhState_code)"/>
          </a>
          <a title="售中支持状态">
            <a-badge class="login-form-forgot" :style="productState(record.szzcState_code)"/>
          </a>
           <a title="合同结算状态">
            <a-badge  class="login-form-forgot" :style="productState(record.htjsState_code)"/>
          </a>
        </template>
      </span>
      <span slot="action" slot-scope="text, record">
            <template>
              <a @click="handleView(record.id, 'view')" title="查看">
                <a-icon type="eye" style="color: blue" />
              </a>
              <span v-action:SYS_ADMINISTRATOR,Personnel_Administrator>
              <!-- <a-divider type="vertical" /> -->
                <a @click="handleEdit(record.id, 'add')" title="编辑" style="margin-left:8px">
                  <a-icon type="edit" style="color: #69aa46" />
                </a>
                <!-- <a-divider type="vertical" /> -->
                <a @click="handleDelete(record.person_id)" title="删除" style="margin-left:8px">
                  <a-icon type="delete" style="color: #ff6347" />
                </a>
              </span>
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
  components: { STable },
    props: ["SOURCE","XMSTATE"],
  mixins: [queryList],
  data() {
    return {
      // 表头
      columns: [
         {
          title: "序号",
          dataIndex: "num",
          // width: 30,
        },
        {
          title: "销售合同号",
          dataIndex: "coding",
          sorter: (a, b) => a.coding.length - b.coding.length ,
          // ellipsis: true,
          //  width: 130,
        },
        {
          title: "合同甲方全称",
          dataIndex: "partyaName",
          sorter: (a, b) => a.partyaName.length - b.partyaName.length ,
          // ellipsis: true,
          //  width: 100
        },
        {
          title: "项目名称",
          dataIndex: "projectName",
          sorter: (a, b) => a.projectName.length - b.projectName.length ,
          // ellipsis: true,
          // width: 100
        },
         {
          title: "销售合同金额",
          dataIndex: "money",
          sorter: (a, b) => a.money - b.money ,
          // width: 100
        },
         {
          title: "关联跟单",
          dataIndex: "xmgd_coding",
          sorter: (a, b) => a.xmgd_coding.length - b.xmgd_coding.length ,
          // ellipsis: true,
          // width: 90
        },
         {
          title: "来源",
          dataIndex: "source_name",
         sorter: (a, b) => a.source_name - b.source_name ,
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
          title: "合同质保期",
          dataIndex: "warranty",
          customRender: function(val) {
            return val ? moment(val).format('YYYY-MM-DD') : ''
            },
          sorter: (a, b) => a.warranty - b.warranty ,
        },
        {   
          title: "最后一次收款时间",
          dataIndex: "lastskdate",
          customRender: function(val) {
            return val ? moment(val).format('YYYY-MM-DD') : ''
            },
          sorter: (a, b) => a.warranty - b.warranty ,
        },
        {
          title: "状态",
          dataIndex: "state",
          width: "90px",
          scopedSlots: { customRender: "state" },
        },
        {
          title: "操作",
          dataIndex: "action",
          width: "100px",
          scopedSlots: { customRender: "action" },
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
    this.path.delete_path = prefix + "/contract/revoke";
  },
  methods: {
     productState(code) {
      var stype = "";
      this.XMSTATE.forEach((item) => {
        if(code==undefined){
          stype = "border:1px solid #d9d9d9;"
        }else if(code==item.code){
          stype = "background-color:"+item.colorCode;
        }
      })
       return stype;
    },
    handleDelete(id) {
      handle_delete(this, id, this.path.delete_path);
    }
  }
};
</script>
<style>
 .login-form-forgot {
    width: 10px;
    height: 10px;
    display: inline-block;
    vertical-align: middle;
    border-radius: 50%;
    line-height: inherit;
    margin: 8px 8px 0 0 ;
  }
</style>
