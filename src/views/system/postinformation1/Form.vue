<template>
  <a-spin :spinning="spinning">
    <a-card :body-style="{padding: '24px 32px'}" :bordered="false" title="人员管理" class="card">
      <a-form :form="form" class="form" @submit="handleSubmit">
        <a-row class="form-row" :gutter="16">
          <a-col :xl="{ span: 8, offset: 3 }" :lg="{ span: 12 }" :md="{ span: 12 }" :sm="24">
            <a-form-item label="所在部门">
              <a-input :readOnly="true" v-decorator="['organ_name',{rules: [{required: true,message: '请输入所在部门',whitespace: true}]}]"/>
              <a-input hidden v-decorator="['organ_id']" />
            </a-form-item>
          </a-col>
          <a-col :xl="{ span: 8, offset: 3 }" :lg="{ span: 12 }" :md="{ span: 12 }" :sm="24">
            <a-form-item label="员工编号">
              <a-input @blur.prevent="onchange_unique"
                v-decorator="['loginName',{rules: [{required: true,message: '请输入员工编号',whitespace: true,}]}]"/>
            </a-form-item>
          </a-col>
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
          <a-col :xl="{ span: 24, offset: 3  }" :lg="{ span: 24 }" :md="{ span: 24 }" :sm="24">
            <a-form-item label="任职岗位">
              <a-select @change="changePost" mode="multiple" size="default" placeholder="请选择岗位" style="width: 600px"
                v-decorator="['posts',{rules: [{required: true,type: 'array',message: '请选择岗位',whitespace: true}]}]">
                <a-select-option v-for="type in initposts" :key="type.id" :value="type.id">{{type.name}}</a-select-option></a-select>
            </a-form-item>
          </a-col>
          <a-col :xl="{ span: 24, offset: 3  }" :lg="{ span: 24 }" :md="{ span: 24 }" :sm="24"
          ><a-form-item label="关联角色"
            ><a-checkbox-group disabled v-model="checkedList" :options="plainOptions" />
          </a-form-item>
        </a-col>
        </a-row>
        <a-form-item :wrapperCol="{ span: 24 }" style="text-align: center">
          <a-button htmlType="submit" type="primary">保存</a-button>
          <a-button style="margin-left: 8px" @click="back">返回</a-button>
        </a-form-item>
      </a-form>
    </a-card>
  </a-spin>
</template>
<script>
import { queryService, toService,back_to_list, save } from "@/api/manage";

export default {
  name: "Postinformation_Form",
  props: ["formId","type","POSTTYPE"],
  data() {
    return {
      form: this.$form.createForm(this),
      spinning: true,
      path: {},
      checkedList: [],
      plainOptions: [],
    };
  },
  created() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.edit_path = prefix + "/postinformation/" + this.type;
    this.path.save_path = prefix + "/postinformation/save";
    this.path.getUniquePath = prefix + "/postinformation/validationUnique";
    this.path.resetPath =  prefix + "/postinformation/reset";
    this.path.getPost = prefix + "/post/get";
    this.path.getRole = prefix + "/post/getRole";
  },
  mounted() {
     queryService(this.path.edit_path, { id: this.formId }).then(res => {
      this.form.setFieldsValue(res.result);
        return res.result;
      });
    this.spinning = false;
  },
  methods: {
    handleSubmit(e) {
      e.preventDefault();
      save(this, this.path.save_path);
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
        id: this.person_id ? this.person_id : "xxxxxxxxxxxx",
        key: id,
        value: value,
        error: error,
      };
      this.memberLoading = true;
      queryService(this.path.getUniquePath, data).then((res) => {
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
    changePost(value) {
      const data = {};
      if (value.length == 0) {
        data.posts = ["xxxxxxxxxxxxxx"];
      } else {
        data.posts = value;
      } 
      queryService(this.path.getRole, data).then((res) => {
        this.plainOptions = res.result;
        this.checkedList = res.result;
      });
    },
    reset() {
      const data = {
        id: this.formId,
      };
      let that = this;
      this.$confirm({
        title: "提示",
        content: "真的要重置密码吗 ?",
        onOk() {
          toService("post", that.path.resetPath, data).then((res) => {
            that.form.setFieldsValue({
              pwd: res.result,
            });
            that.$notification.success({
              message: "密码重置成功",
              description: "密码重置成功",
            });
          });
        },
        onCancel() {},
      });
    },
    
  }
};
</script>
