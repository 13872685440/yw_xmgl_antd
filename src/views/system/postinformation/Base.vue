<template>
  <a-spin :spinning="spinning">
    <a-form :form="form" class="form" @submit="handleSubmit">
      <a-row class="form-row" :gutter="16">
          <a-col :xl="{ span: 8, offset: 3 }" :lg="{ span: 12 }" :md="{ span: 12 }" :sm="24">
             <a-form-item label="所在部门">
              <a-input :readOnly="true" v-decorator="['organ_name',{rules: [{required: true,message: '请输入所在部门',whitespace: true}]}]"/>
              <a-input hidden v-decorator="['organ_id']" />
              <a-input hidden v-decorator="['id']" />
            </a-form-item>
          </a-col>
           <a-col :xl="{ span: 8, offset: 3 }" :lg="{ span: 12 }" :md="{ span: 12 }" :sm="24">
            <a-form-item label="员工编号">
              <a-input @blur.prevent="onchange_unique"
                v-decorator="['loginName',{rules: [{required: true,message: '请输入员工编号',whitespace: true,}]}]"/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row>
         <a-col :xl="{ span: 8, offset: 3  }" :lg="{ span: 12 }" :md="{ span: 12 }" :sm="24">
            <a-form-item label="姓名">
              <a-input v-decorator="['person_name',{rules: [{required: true,message: '请输入姓名',whitespace: true}]}]"/>
              <a-input hidden v-decorator="['person_id']" />
            </a-form-item>
          </a-col>
          <a-col :xl="{ span: 8, offset: 3  }" :lg="{ span: 12 }" :md="{ span: 12 }" :sm="24">
            <a-form-item label="手机号码">
              <a-input @blur.prevent="onchange_unique"  
                v-decorator="['phone',{rules: [{required: true,message: '请输入手机号码',whitespace: true}]}]"/>
            </a-form-item> 
          </a-col>
        </a-row>
        <a-row>
           <a-col :xl="{ span: 8, offset: 3  }" :lg="{ span: 12 }" :md="{ span: 12 }" :sm="24">
            <a-form-item label="任职状态">
              <a-select placeholder="请选择任职状态" v-decorator="[
                'leaf_code', { rules: [{required: true,message: '请选择任职状态',whitespace: true}]}]">
                <a-select-option v-for="type in POSTTYPE" :key="type.code" :value="type.code" >{{ type.name }}</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :xl="{ span: 8, offset: 3  }" :lg="{ span: 12 }" :md="{ span: 12 }" :sm="24">
             <a-form-item v-if="formId != ''" label="重置密码">
                <a-input :readOnly="true" v-decorator="['pwd']">
                  <a-icon slot="addonAfter" type="reload" style="color: red; cursor: pointer" @click="reset()" />
                </a-input>
              </a-form-item>
          </a-col>
        </a-row>
      <a-form-item :wrapperCol="{ span: 24 }" style="text-align: center">
        <a-button htmlType="submit" type="primary">保存</a-button>
      </a-form-item>
    </a-form>
  </a-spin>
</template>
<script>
import { queryService, toService, back_to_list, saveService } from "@/api/manage";
import { MODEL_SYSTEM } from "@/store/mutation-types";

const savePath = "/" + MODEL_SYSTEM + "/postinformation/save";
const getUniquePath = "/" + MODEL_SYSTEM + "/postinformation/validationUnique";
const resetPath = "/" + MODEL_SYSTEM + "/postinformation/reset";

export default {
  name: "Post_Base_Form",
  props: [
    "formId",
    "type",
    "POSTTYPE",
    "base",
  ],
  data() {
    return {
      form: this.$form.createForm(this),
      spinning: true,
      memberLoading: false,
      data: [],
      cansave: true,
      photo_url: "",
    };
  },
  mounted() {
    this.form.setFieldsValue(this.base);
    this.spinning = false;
  },
  methods: {
    handleSubmit(e) {
      e.preventDefault();
      this.form.validateFields((err, values) => {
        if (!err) {
          saveService(savePath, values)
            .then((res) => {
              this.$notification.success({
                message: "保存成功",
                description: "",
              });
              this.$emit("changeForm", {
                name: "base",
                data: res.result,
              });
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
    onchange_unique(e) {
      const { id, value } = e.target;
      var error = "";
      if (id === "phone") {
        error = "手机号码重复";
      } else {
        error = "员工编号重复";
      }
      const data = {
        id: this.base.person_id ? this.base.person_id : "xxxxxxxxxxxx",
        key: id,
        value: value,
        error: error,
      };
      this.memberLoading = true;
      queryService(getUniquePath, data).then((res) => {
        if (res.result && res.result.error) {
          var errordata = {};
          errordata[id] = {
            value: value,
            errors: [
              {
                message: res.result.error,
                field: id,
              },
            ],
          };
          this.form.setFields(errordata);
        }
        this.memberLoading = false;
      });
    },
    reset() {
      const parameter = {
        id: this.formId,
      };
      let that = this;
      this.$confirm({
        title: "提示",
        content: "真的要重置密码吗 ?",
        onOk() {
          toService("post", resetPath, parameter).then((res) => {
            that.form.setFieldsValue({
              pwd: res.result,
            });
            this.$notification.success({
              message: "密码重置成功",
              description: "密码重置成功",
            });
          });
        },
        onCancel() {},
      });
    },
  },
};
</script>

<style lang="less" scoped>
.card {
  margin-bottom: 24px;
}
.ant-upload.ant-upload-select.ant-upload-select-picture-card {
  width: 150px !important;
  height: 200px;
}
.ant-upload-select-picture-card i {
  font-size: 32px;
  color: #999;
}

.ant-upload-select-picture-card .ant-upload-text {
  margin-top: 8px;
  color: #666;
}
</style>
