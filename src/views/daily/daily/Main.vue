<template>
<div class="gutter-example">
  <a-row :gutter="16">
    <a-col xs="24" sm="24" md="24" lg="24" xl="4" class="gutter-row" :span="4">
    
    <div class="gutter-box-date">重要记事</div>
    <div class="gutter-box" style="padding-top:15px">
      <a-list :grid="{ gutter: 16, column: 1 }" :data-source="signdata">
        <a-list-item slot="renderItem" slot-scope="item">
            <div class="gutter-box-title" >{{item.title}}</div>
        </a-list-item>
      </a-list>
    </div>
    </a-col>
    <a-col xs="24" sm="24" md="24" lg="24" xl="14" class="gutter-row" :span="14">
      <div class="gutter-box-btn">
        <a-button v-if="isadd"  type="primary" icon="plus" v-action:SYS_ADMINISTRATOR   @click="$refs.createDaily.add('')">新增</a-button>
        <a-button v-if="!isadd" disabled type="primary" icon="plus" v-action:SYS_ADMINISTRATOR   @click="$refs.createDaily.add('')">新增</a-button>
      </div>
      <div class="gutter-box">
        <a-calendar @select="onSelect" @panelChange="onChange">
          <ul slot="dateCellRender" slot-scope="value" class="events">
              <li v-for="(item) in getListData(value)" :key="item.title">
                <a-badge v-if="item.isSign===1" slot="avatar" status="error" :text="item.title" />
                <a-badge v-if="item.isSign===0" slot="avatar" status="success" :text="item.title"  />
              </li>
              <li v-if="getListCount(value)>2">...</li>
          </ul>
        </a-calendar>
        <create-daily ref="createDaily" :dailyDate="dailyDate"  @ok="handleOk"/>
      </div>
    </a-col>
    <a-col xs="24" sm="24" md="24" lg="24" xl="6" class="gutter-row" :span="6">
      <div class="gutter-box-date">{{dailyDate1}}</div>
      <a-list :grid="{ gutter: 16, column: 1 }" :data-source="daydata">
        <a-list-item slot="renderItem" slot-scope="item">
          <a-card :title="item.title">
            <a slot="extra" @click="$refs.createDaily.add(item.id)" title="编辑"> <a-icon type="edit" style="color:#69aa46" /></a>
            <a slot="extra" @click="handleDelete(item.id)" title="删除"><a-icon type="delete" style="color:#FF6347" /></a>
            <span class="gutter-box-day-span">{{item.content}}</span>
          </a-card>
        </a-list-item>
      </a-list>
    </a-col>
  </a-row>
  </div>
</template>
<script>
import moment from "moment";
import 'moment/locale/zh-cn';
moment.locale('zh-cn');
import { queryService,toService } from "@/api/manage";


import createDaily from "./modules/CreateDaily";


export default {
  name: "diaryCalendar",
   components: {
    createDaily,
  },
  data() {
    return {
      spinning: true,
      dailyDate:moment(Date()).format("YYYY-MM-DD"),
      dailyDate1:moment(Date()).format("YYYY年MM月DD"),
      signdata: [],
      daydata:[],
      monthdata:[],
      isadd:true,
      path: {},
      loading: false,
      busy: false,
    }
  },
  mounted() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.sign_list = prefix + "/daily/getSignlist";
    this.path.day_list = prefix + "/daily/getDaylist";
    this.path.month_list = prefix + "/daily/getMonthlist";
    this.path.delete_path = prefix + "/daily/delete";

    this.renderSign();
    this.renderDay();
    this.renderMonth();
    this.spinning = false;
  },
  methods: {
    onChange(value){
      this.isadd = true;
      this.dailyDate = moment(value).format("YYYY-MM-DD");
      this.dailyDate1 = moment(value).format("YYYY年MM月DD"),
      this.renderTable();
      if(Date.parse(this.dailyDate) < Date.parse(moment(new Date()).format("YYYY-MM-DD"))){
        this.isadd = false;
      }
      
    },
    onSelect(value){
      this.isadd = true;
      if(Date.parse( moment(value).format("YYYY-MM-DD")) < Date.parse(moment(new Date()).format("YYYY-MM-DD"))){
          this.isadd = false;
      }
      this.dailyDate = moment(value).format("YYYY-MM-DD");
      this.dailyDate1 = moment(value).format("YYYY年MM月DD"),
      this.renderDay();
    },
     handleOk() {
      this.renderTable();
    },
    renderTable(){
      this.renderSign();
      this.renderDay();
      this.renderMonth();
    },
    async renderSign() {
        await queryService(this.path.sign_list, {dailyDate:this.dailyDate}).then((res) => {
          this.signdata = res.result;
        });
    },
    async renderDay() {
        await  queryService(this.path.day_list, {dailyDate:this.dailyDate}).then((res) => {
          this.daydata = res.result;
        });
    },
    async renderMonth() {
      await queryService(this.path.month_list, {dailyDate:this.dailyDate}).then((res) => {
        this.monthdata = res.result;
       });
    },
    getListData(value) {
      let listData = new Array();
      let year = value.year(); // 年
      let month = value.month() + 1; // 月
      let day = value.date(); // 日
      let va = year+"-"+month+"-"+day;
      va = moment(va).format("YYYY-MM-DD")
      this.monthdata.forEach((v) => {
        let date = moment(v.dailyDate).format("YYYY-MM-DD")
        if(listData.length<2 ){
          if(va==date){
            listData.push(v);
          }
        }
      });
      return listData || [];
    },
    getListCount(value){
      let count = 0;
      this.monthdata.forEach((v) => {
        switch (value.date()) {
          case v.day:
            count++;
            break;
          default:
        }
      });
     return count;
    },
    getMonthData(value) {
      if (value.month() === 8) {
        return 1394;
      }
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
  }
};
</script>
<style scoped>
 .events {
  list-style: none;
  margin: 0;
  padding: 0;
}
.events .ant-badge-status {
  overflow: hidden;
  white-space: nowrap;
  width: 100%;
  text-overflow: ellipsis;
  font-size: 12px;
}
.ant-fullcalendar-content {
    position: absolute;
    bottom: 0;
    left: 0;
    width: 100%;
}

.demo-infinite-container {
  border: 1px solid #e8e8e8;
  border-radius: 4px;
  overflow: auto;
  padding: 8px 24px;
  height: 300px;
}
.demo-loading-container {
  position: absolute;
  bottom: 40px;
  width: 100%;
  text-align: center;
}


.gutter-example >>> .ant-row > div {
  background: transparent;
  border: 0;
}
.gutter-box {
  background: #fff;
}

.gutter-box-btn{
  background:#fff;
  margin-bottom:10px;
  height:50px;
  padding:10px 0 0 10px;
  
}
.gutter-box-date{
  background:#fff;
  margin-bottom:10px;
  height:50px;
  padding:10px 0 0 10px;
  font-size: 16px;
  font-weight: 600;
  text-align:center;
}

.gutter-box-title{
  background-color: #f5222d;
  margin:  0 10px 0 10px;
  height:30px;
  padding:4px 0 0 8px;
  font-size: 13px;
  font-weight:500;
  color:#FAFAD2;
  text-align:left;
  border-radius: 50px;
overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
}
.ant-list-item-meta-avatar {
    margin-right: 0px;
}
.ant-list-bordered {
   border: 0px;
}

.gutter-title{
  font-size: 16px;
  font-weight: 600;
  text-align:center;
  line-height: 1.143;
  padding-left:10px;
}

.ant-divider-horizontal {
    margin: 10px 0;
}

 .login-form-forgot {
    width: 10px;
    height: 10px;
    display: inline-block;
    vertical-align: middle;
    border-radius: 50%;
    line-height: 1.143;
    margin: 8px 8px 0 0 ;
  }

  .gutter-box-day-span{
    font-size: 12px;
    padding: 7px 0 7px 16px;
    line-height: 1.143;
  }

</style>