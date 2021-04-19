<template>
  <span>
    <a-card>
      <span slot="title" class="card">
        <div>{{ base.person_name }}</div>
        <div style="color: rgba(0, 0, 0, 0.45); font-size: 14px; padding-top: 5px">
          <span>{{ base.organ_name }}</span> 
          <a-divider type="vertical" />
          <span v-if="base.post_names">
            <template v-for="name in base.post_names">{{ name }}</template>
          </span>
        </div>
      </span>
      <a-form :form="form" class="form">
      <a-row class="form-row" :gutter="16">
        <a-col :xl="{ span: 8, offset: 3 }" :lg="{ span: 12 }" :md="{ span: 12 }" :sm="24">
          <a-form-item label="所在部门">
            <a-input v-decorator="['organ_name']"/>
          </a-form-item>
        </a-col>
        <a-col :xl="{ span: 8, offset: 3 }" :lg="{ span: 12 }" :md="{ span: 12 }" :sm="24">
          <a-form-item label="员工编号">
            <a-input v-decorator="['loginName']"/>
          </a-form-item>
        </a-col>
        <a-col :xl="{ span: 8, offset: 3  }" :lg="{ span: 12 }" :md="{ span: 12 }" :sm="24">
          <a-form-item label="姓名">
            <a-input v-decorator="['person_name']"/>
          </a-form-item>
        </a-col>
        <a-col :xl="{ span: 8, offset: 3  }" :lg="{ span: 12 }" :md="{ span: 12 }" :sm="24">
          <a-form-item label="手机号码">
            <a-input  v-decorator="['phone']"/></a-form-item> 
        </a-col>
        <a-col :xl="{ span: 8, offset: 3  }" :lg="{ span: 12 }" :md="{ span: 12 }" :sm="24">
          <a-form-item label="任职状态">
            <a-input v-decorator="['leaf_code']"/>
          </a-form-item>
        </a-col>
      </a-row>
      </a-form>
    </a-card>
  </span>
</template>

<script>
import {back_to_list } from "@/api/manage";


export default {
  name: "Post_Base_View",
  props: [
    "formId",
    "type",
    "base",
  ],
  data() {
    return {
      form: this.$form.createForm(this),
    };
  },
  mounted() {
    this.form.setFieldsValue(this.base);
    this.spinning = false;
  },
  methods: {
    handleSubmit(e) {
      e.preventDefault();
    },
    back() {
      back_to_list(this);
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
