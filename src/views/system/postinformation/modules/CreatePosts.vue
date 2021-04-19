<template>
  <a-modal
    :title="title"
    :width="600"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleSubmit"
    @cancel="handleCancel"
  >
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <a-form-item>
          <a-input v-decorator="['id']" hidden />
        </a-form-item>
        <a-form-item>
          <a-input v-decorator="['person_id']" hidden />
        </a-form-item>
        <a-form-item label="类型" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-radio-group
            v-decorator="[
              'property',
              {
                rules: [
                  {
                    required: true,
                    message: '请选择类型',
                    whitespace: true,
                  },
                ],
              },
            ]"
          >
            <template v-for="type in PROS">
              <a-radio :key="type.code" :value="type.code">{{ type.desc }}</a-radio>
            </template>
          </a-radio-group>
        </a-form-item>
        <a-form-item label="任职单位/部门" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input
            v-decorator="[
              'organ_name',
              { rules: [{ required: true, message: '请输入任职单位/部门' }] },
            ]"
          />
        </a-form-item>
        <a-form-item label="职务" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input
            v-decorator="['duty', { rules: [{ required: true, message: '请输入职务' }] }]"
          />
        </a-form-item>
        <a-form-item label="开始任职" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-date-picker
            style="width: 100%"
            format="YYYY-MM-DD"
            v-decorator="[
              'entrytime',
              { rules: [{ required: true, message: '请选择开始任职时间' }] },
            ]"
          />
        </a-form-item>
        <a-form-item label="结束任职" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-date-picker
            style="width: 100%"
            format="YYYY-MM-DD"
            v-decorator="[
              'leaftime',
              { rules: [{ required: true, message: '请选择结束任职时间' }] },
            ]"
          />
        </a-form-item>
        <a-form-item label="序号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input-number
            v-decorator="[
              'xh',
              {
                rules: [
                  {
                    required: true,
                    type: 'number',
                    message: '请输入数字',
                    whitespace: true,
                  },
                ],
              },
            ]"
          />
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>
<script>
import { queryService, saveService } from "@/api/manage";
import moment from "moment";

export default {
  props: ["person_id"],
  data() {
    return {
      labelCol: {
        xs: { span: 24 },
        sm: { span: 7 },
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 13 },
      },
      visible: false,
      confirmLoading: false,

      form: this.$form.createForm(this),

      path: {},
      title: "",
      PROS: [
        { code: "PRO001", desc: "司内" },
        { code: "PRO002", desc: "司外" },
      ],
    };
  },
  created() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.edit_path = prefix + "/postinformation/edit_postinformation";
    this.path.save_path = prefix + "/postinformation/save_postinformation";
  },
  methods: {
    add(id) {
      this.visible = true;
      if (id) {
        this.confirmLoading = true;
        queryService(this.path.edit_path, { id: id }).then((res) => {
          this.form.setFieldsValue(res.result);
          if (res.result.leaftime) {
            this.form.setFieldsValue({
              leaftime: moment(res.result.leaftime),
            });
          }
          if (res.result.entrytime) {
            this.form.setFieldsValue({
              entrytime: moment(res.result.entrytime),
            });
          }
          this.confirmLoading = false;
        });
      } else {
        this.form.resetFields();
      }
    },
    handleSubmit() {
      const that = this;
      this.confirmLoading = true;
      this.form.validateFields((errors, values) => {
        if (!errors) {
          values.person_id = this.person_id;
          values.entrytime = moment(values.entrytime).format("YYYY-MM-DD");
          values.leaftime = moment(values.leaftime).format("YYYY-MM-DD");
          saveService(this.path.save_path, values)
            .then((res) => {
              console.log(res.code);
              if (res.code == "400") {
                that.$notification.error({
                  message: res.message,
                  description: res.result,
                });
              } else {
                that.$notification.success({
                  message: "保存成功",
                  description: "",
                });
              }
              that.$emit("ok");
              that.visible = false;
              that.confirmLoading = false;
            })
            .catch((error) => {
              that.confirmLoading = false;
              console.log(error);
            });
        } else {
          this.confirmLoading = false;
        }
      });
    },
    handleCancel() {
      this.visible = false;
    },
  },
};
</script>
