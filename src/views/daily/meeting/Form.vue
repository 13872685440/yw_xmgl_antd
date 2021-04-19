<template>
  <a-spin :spinning="spinning">
    <a-card
      :body-style="{ padding: '24px 32px' }"
      :bordered="false"
      title="会议记录管理"
      class="card"> 
      <a-form :form="form" class="form" @submit="handleSubmit">
        <a-row class="form-row" :gutter="16">
          <a-col :xl="{span: 7, offset: 1}" :lg="{span: 8}" :md="{span: 12}" :sm="24">
            <a-form-item label="标题">
              <a-input v-decorator="['title', {rules: [{ required: true, message: '请输入标题', whitespace: true}]}]"/>
              <a-input hidden v-decorator="['id']" />
            </a-form-item>
          </a-col>
          <a-col :xl="{span: 7, offset: 1}" :lg="{span: 8}" :md="{span: 12}" :sm="24">
            <a-form-item label="会议日期">
              <a-date-picker style="width: 100%"  format="YYYY-MM-DD" v-decorator="['meetingDate']"/>
            </a-form-item>
          </a-col>
          <a-col :xl="{span: 7, offset: 1}" :lg="{span: 8}" :md="{span: 12}" :sm="24">
            <a-form-item label="时间">
              <a-time-picker style="width:130px"  format="HH:mm" v-decorator="['stime']" />
              至
              <a-time-picker style="width:130px"  format="HH:mm" v-decorator="['etime']" />
            </a-form-item>
          </a-col>
          <a-col :xl="{span: 7, offset: 1}" :lg="{span: 8}" :md="{span: 12}" :sm="24">
            <a-form-item label="会议主持人">
              <a-input v-decorator="['host', {rules: [{ message: '请输入会议主持人', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
          <a-col :xl="{span: 7, offset: 1}" :lg="{span: 8}" :md="{span: 12}" :sm="24">
            <a-form-item label="会议召开地址">
              <a-input v-decorator="['addr', {rules: [{ message: '请输入会议召开地址', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row class="form-row" :gutter="16">
           <a-col  :xl="{span: 23, offset: 1}" :lg="{span: 24}" :md="{span: 24}" :wrapperCol="{ span: 24 }">
            <a-form-item label="会议内容">
              <a-input  type="textarea" :rows="5" v-decorator="['content', {rules: [{ message: '请输入会议内容', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-form-item :wrapperCol="{ span: 24 }" style="text-align: center">
          <a-button  htmlType="submit"  type="primary">保存</a-button>
          <a-button style="margin-left: 8px" @click="back">返回</a-button>
        </a-form-item>
      </a-form>
    </a-card>
  </a-spin>
</template>
<script>
import { queryService, back_to_list, saveService } from "@/api/manage";
import moment from "moment";

export default {
  name: "Meeting_Form",
  props: ["formId","type",],
  data() {
    return {
      form: this.$form.createForm(this),
      spinning: true,
      path: {},
      errordata:{},
    };
  },
  created() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.edit_path = prefix + "/meeting/" + this.type;
    this.path.save_path = prefix + "/meeting/save";
  },
  mounted() {
    queryService(this.path.edit_path, { id: this.formId }).then((res) => {
      if(res.result.stime!=undefined){
        res.result.stime=moment(res.result.stime, 'HH:mm')
      }
      if(res.result.etime!=undefined){
        res.result.etime=moment(res.result.etime, 'HH:mm')
      }
        this.form.setFieldsValue(res.result);
       return res.result;
    });
    this.spinning = false;
  },
  methods: {
    handleSubmit(e) {
      e.preventDefault();
      this.form.validateFields((err, values) => {
        if(values.meetingDate!=null){
          values.meetingDate = moment(values.meetingDate).format("YYYY-MM-DD");
        }
        if(values.stime!=null){
          values.stime = moment(values.stime).format("HH:mm");
        }
        if(values.etime!=null){
          values.etime = moment(values.etime).format("HH:mm");
        }
        values.id=this.formId;
        if (!err) {
          saveService(this.path.save_path, values)
            .then(() => {
              this.$notification.success({
                message: "保存成功",
                description: "",
              });
              back_to_list(this);
              this.spinning = false;
            })
            .catch(() => {
              this.spinning = false;
            });
        } else {
          this.spinning = false;
        }
      });
    },
    back() {
      back_to_list(this);
    },
  },
};
</script>

<style lang="less" scoped>
.account-settings-info-main {
  width: 100%;
  display: flex;
  height: 100%;
  overflow: auto;

  &.mobile {
    display: block;

    .account-settings-info-left {
      border-right: unset;
      border-bottom: 1px solid #e8e8e8;
      width: 100%;
      height: 50px;
      overflow-x: auto;
      overflow-y: scroll;
    }
    .account-settings-info-right {
      padding: 20px 40px;
    }
  }

  .account-settings-info-left {
    border-right: 1px solid #e8e8e8;
    width: 224px;
  }

  .account-settings-info-right {
    flex: 1 1;
    padding: 8px 40px;

    .account-settings-info-title {
      color: rgba(0, 0, 0, 0.85);
      font-size: 20px;
      font-weight: 500;
      line-height: 28px;
      margin-bottom: 12px;
    }
    .account-settings-info-view {
      padding-top: 12px;
    }
  }
}
</style>
