<template>
  <a-spin :spinning="spinning">
    <a-card :body-style="{ padding: '24px 32px' }" :bordered="false" title="请假流程管理" class="card"> 
      <a-form :form="form" class="form" @submit="handleSubmit">
            <a-form-item label="请假类型" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-select placeholder="请选择" v-decorator="['leavetType_code',{ rules: [{required: true,message: '请选择',whitespace: true,}]}]" >
                <a-select-option v-for="item in LEAVETTYPE" :key="item.code" :value="item.code">{{ item.name }}</a-select-option>
              </a-select>
            </a-form-item>
            <a-form-item label="开始日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-date-picker format="YYYY-MM-DD" :disabled-date="disabledStartDate" @change="handleSDChange" 
                v-decorator="['startDate',{ rules: [{ type: 'object',required: true,message: '请选择',whitespace: true,}]}]"/>
              <a-select style="width: 100px;padding-left:8px"  placeholder="请选择" @change="handleSTChange" 
                v-decorator="['startTime',{ rules: [{required: true,message: '请选择',whitespace: true,}]}]" >
                <a-select-option value="1">上午</a-select-option>
                <a-select-option value="2">下午</a-select-option>
              </a-select>
            </a-form-item>
            <a-form-item label="结束日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-date-picker  format="YYYY-MM-DD" :disabled-date="disabledEndDate" @change="handleEDChange" 
                v-decorator="['endDate',{ rules: [{type: 'object',required: true,message: '请选择',whitespace: true,}]}]"/>
              <a-select style="width: 100px;padding-left:8px"placeholder="请选择"  @change="handleETChange" 
                v-decorator="['endTime',{ rules: [{required: true,message: '请选择时间',whitespace: true,}]}]" >
                <a-select-option value="1">上午</a-select-option>
                <a-select-option value="2">下午</a-select-option>
              </a-select>
            </a-form-item>
            <a-form-item label="时长" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input :disabled="true"  v-decorator="['duration', {rules: [{required: true, message: '请选择时间', whitespace: true}]}]"/>
            </a-form-item>
            <a-form-item label="说明" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input  type="textarea" :rows="5" v-decorator="['explain']"/>
            </a-form-item>
          </a-col>
           <!-- <a-col  :xl="{span: 23, offset: 1}" :lg="{span: 24}" :md="{span: 24}" :wrapperCol="{ span: 24 }">
            <a-form-item label="审核人">
              <a-inputv-decorator="['content', {rules: [{ message: '请输入内容', whitespace: true}]}]"/>
            </a-form-item>
          </a-col> -->
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
  name: "Leave_Form",
  props: ["formId","type","LEAVETTYPE"],
  data() {
    return {
      form: this.$form.createForm(this),
      spinning: true,
      path: {},
      errordata:{},
      labelCol:{ lg: { span: 5 }, sm: { span: 5 }},
      wrapperCol:{ lg: {span: 15 }, sm: { span: 17 }}
    };
  },
  created() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.edit_path = prefix + "/leave/" + this.type;
    this.path.save_path = prefix + "/leave/save";
  },
  mounted() {
    if(this.formId!=""){
    queryService(this.path.edit_path, { id: this.formId }).then((res) => {
      this.form.setFieldsValue(res.result);
       return res.result;
    });
    }else{
      this.form.setFieldsValue({
        startDate:moment(new Date()),
        endDate:moment(new Date()),
        startTime:"1",
        endTime:"1",
        duration:"0.5"
      });
    }
    this.spinning = false;
  },
  methods: {
    disabledStartDate(startValue) { 
      const endValue = this.endValue;
      if(startValue && startValue <moment().subtract(1, "days")){
        return true;
      }
      if (!startValue || !endValue) {
        return false;
      }
      if(startValue.valueOf() > endValue.valueOf()){
        return true;
      }
    },
    disabledEndDate(endValue) {
      const startValue = this.startValue;
      if (!endValue || !startValue) {
        return false;
      }
      return startValue.valueOf() >= endValue.valueOf();
    },
    handleSDChange(value) {
      this.handleChange('startDate',value);
    },
    handleSTChange(value) {
      this.handleChange('startTime',value);
    },
    handleEDChange(value) {
      this.handleChange('endDate',value);
    }, 
    handleETChange(value) {
      this.handleChange('endTime',value);
    },
    handleChange(id,value) {
      let form = this.form.getFieldsValue();
      let startDate =  moment(id=='startDate'?value:form.startDate).format('YYYY-MM-DD');
      let startTime = id=='startTime'?Number(value):Number(form.startTime);
      let endDate =  moment(id=='endDate'?value:form.endDate).format('YYYY-MM-DD');
      let endTime = id=='endTime'?Number(value):Number(form.endTime);
      let duration = null;

      if(startDate==endDate){
        if((startTime==2 && endTime==1)){
             this.$notification.error({ message: "错误", description: "选择错误，请重新选择",});
             return false;
        }
            duration = startTime == endTime?0.5:1;
      }else{
        var  aa = Date.parse(startDate);
        var  bb = Date.parse(endDate);
        duration =(bb - aa)/(1*24*60*60*1000);
        if(startTime == endTime){
          duration = Number(duration)+0.5;
        } if((startTime==1 && endTime==2) || (startTime==2 && endTime==1) ){
          duration = Number(duration)+1;
        }
      }
      this.form.setFieldsValue({duration:String(duration)});
    },
     handleSubmit(e) {
        e.preventDefault();
      // var that=this;
      this.spinning = true;
      this.form.validateFields((err, values) => {
        if (!err) {
          values.startDate = moment(values.startDate).format('YYYY-MM-DD');
          values.endDate = moment(values.endDate).format('YYYY-MM-DD');
          saveService(this.path.save_path, values).then(() => {
            back_to_list(this);
            this.$notification.success({
              message: "保存成功",
              description: ""
            })
          }).catch(error => {
            this.spinning = false;
            console.log(error);
          }
          );
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