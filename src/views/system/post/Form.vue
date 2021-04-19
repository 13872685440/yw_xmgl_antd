<template>
  <a-spin :spinning="spinning">
    <a-card
      :body-style="{ padding: '24px 32px' }"
      :bordered="false"
      title="岗位管理"
      class="card"
    >
      <a-form :form="form" class="form" @submit="handleSubmit">
        <a-form-item
          label="岗位名称"
          :labelCol="{ lg: { span: 7 }, sm: { span: 7 } }"
          :wrapperCol="{ lg: { span: 5 }, sm: { span: 12 } }"
        >
          <a-input
            v-decorator="[
              'name',
              {
                rules: [{ required: true, message: '请输入岗位名称', whitespace: true }],
              },
            ]"
          />
          <a-input hidden v-decorator="['id']" />
        </a-form-item>
        <a-form-item
          label="序号"
          :labelCol="{ lg: { span: 7 }, sm: { span: 7 } }"
          :wrapperCol="{ lg: { span: 10 }, sm: { span: 17 } }"
        >
          <a-input-number
            :min="1"
            :max="899"
            v-decorator="[
              'xh',
              {
                rules: [
                  {
                    type: 'number',
                    message: '请输入数字',
                    whitespace: true,
                  },
                ],
              },
            ]"
          />
        </a-form-item>
        <a-form-item label="选择组织机构" :labelCol="{ lg: { span: 7 }, sm: { span: 7 } }" :wrapperCol="{ lg: {span: 10 }, sm: { span: 17 }}">
          <a-select mode="multiple" size="default" placeholder="请选择岗位关联组织机构" style="width: 600px"
            v-decorator="['orgs', {rules: [{ required: true, type: 'array', message: '请选择岗位关联组织机构',whitespace: true}]}]">
            <a-select-option v-for="type in ORGS" :key="type.code" :value="type.code" >{{type.name}}</a-select-option >
          </a-select>
        </a-form-item>
        <a-form-item label="选择关联角色" :labelCol="{lg:{span:7},sm:{span: 7}}" :wrapperCol="{lg:{span: 10},sm:{span: 17 }}">
          <a-select mode="multiple" size="default" placeholder="请选择岗位关联角色" style="width: 600px"
            v-decorator="['roles',{rules: [{required: true,type: 'array',message: '请选择岗位关联角色',whitespace: true}]}]">
            <a-select-option v-for="type in ROLES" :key="type.name" :value="type.name">{{type.des}}</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item :wrapperCol="{ span: 24 }" style="text-align: center">
          <a-button htmlType="submit" type="primary">保存</a-button>
          <a-button style="margin-left: 8px" @click="back">返回</a-button>
        </a-form-item>
      </a-form>
    </a-card>
  </a-spin>
</template>
<script>
import { queryService, back_to_list, save } from "@/api/manage";
import { MODEL_SYSTEM } from "@/store/mutation-types";

const editPath = (type) => {
  return "/" + MODEL_SYSTEM + "/post/" + type;
};
const savePath = "/" + MODEL_SYSTEM + "/post/save";

export default {
  name: "Post_Form",
  props: ["formId", "type", "ORGS", "ROLES"],
  data() {
    return {
      form: this.$form.createForm(this),
      spinning: true,
    };
  },
  mounted() {
    queryService(editPath(this.type), { id: this.formId }).then((res) => {
      this.form.setFieldsValue(res.result);
    });
    this.spinning = false;
  },
  methods: {
    handleSubmit(e) {
      e.preventDefault();
      save(this, savePath);
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
</style>
