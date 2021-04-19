<template>
  <a-spin :spinning="spinning">
    <a-card
      :body-style="{ padding: '24px 32px' }"
      :bordered="false"
      title="内部通信管理"
      class="card"> 
      <a-form :form="form" class="form" @submit="handleSubmit">
        <a-row class="form-row" :gutter="16">
          <a-col :xl="{span: 7, offset: 1}" :lg="{span: 8, offset: 3}" :md="{span: 12}" :sm="24">
            <a-form-item label="姓名">
              <a-input v-decorator="['name', {rules: [{ required: true, message: '请输入姓名', whitespace: true}]}]"/>
              <a-input hidden v-decorator="['id']" />
            </a-form-item>
          </a-col>
        <!-- </a-row>
         <a-row class="form-row" :gutter="16"> -->
          <a-col :xl="{span: 7, offset: 1}" :lg="{span: 8, offset: 3}" :md="{span: 12}" :sm="24">
            <a-form-item label="办公电话">
              <a-input v-decorator="['worktel', {rules: [{ required: true, message: '请输入办公电话', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
        <!-- </a-row>
         <a-row class="form-row" :gutter="16"> -->
          <a-col :xl="{span: 7, offset: 1}" :lg="{span: 8}" :md="{span: 12}" :sm="24">
            <a-form-item label="个人电话">
              <a-input  @blur.prevent="onchange_unique" v-decorator="['phone', {rules: [{ required: true, message: '请输入个人电话', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
        <!-- </a-row>
         <a-row class="form-row" :gutter="16"> -->
          <a-col :xl="{span: 7, offset: 1}" :lg="{span: 8}" :md="{span: 12}" :sm="24">
            <a-form-item label="地址">
              <a-input v-decorator="['address', {rules: [{ required: true, message: '请输入地址', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
        <!-- </a-row>
         <a-row class="form-row" :gutter="16"> -->
          <a-col :xl="{span: 7, offset: 1}" :lg="{span: 8}" :md="{span: 12}" :sm="24">
            <a-form-item label="电子邮件">
              <a-input v-decorator="['email', {rules: [{ required: true, message: '请输入电子邮件', whitespace: true}]}]"/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row class="form-row" :gutter="16">
           <a-col  :xl="{span: 23, offset: 1}" :lg="{span: 24}" :md="{span: 24}" :wrapperCol="{ span: 24 }">
            <a-form-item label="备注">
              <a-input  type="textarea" :rows="5" v-decorator="['remark']"/>
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
import { queryService, back_to_list, save } from "@/api/manage";

export default {
  name: "Staffc_Form",
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
    this.path.edit_path = prefix + "/staffc/" + this.type;
    this.path.save_path = prefix + "/staffc/save";
    this.path.getUniquePath = prefix + "/staffc/validationUnique";
  },
  mounted() {
    queryService(this.path.edit_path, { id: this.formId }).then((res) => {
      this.form.setFieldsValue(res.result);
       return res.result;
    });
    this.spinning = false;
  },
  methods: {
    handleSubmit(e) {
      e.preventDefault();
       if(this.errordata.phone!=undefined){
          this.form.setFields(this.errordata);
       }else{
          save(this, this.path.save_path);
       }
    },
    back() {
      back_to_list(this);
    },
     onchange_unique(e) {
      const { id, value } = e.target;
      var error = "个人手机重复";
      const data = {
        key: id,
        value: value,
        error: error,
      };
      this.memberLoading = true;
      this.errordata = {};
      queryService(this.path.getUniquePath, data).then((res) => {
        if (res.result && res.result.error) {
          this.errordata[id] = {
            value: value,
            errors: [
              {
                message: res.result.error,
                field: id,
              },
            ],
          };
          this.form.setFields(this.errordata);
        }
        this.memberLoading = false;
      });
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
