<template>
  <a-spin :spinning="spinning">
    <a-form class="ant-advanced-search-form">
     <a-row :gutter="50">
        <a-col :span="24">
          <a-form-item v-bind="formItemLayout" label="附件">
            <upload-file
              :multiple="true"
              :file_data="file_data"
              :length="isview ? -1 : 100"
            ></upload-file>
          </a-form-item>
        </a-col>
      </a-row>
    </a-form>
  </a-spin>
</template>

<script>
import { editForm } from "@/utils/mixin";
import UploadFile from "@/components/File/UploadFile";

export default {
  name: "Form_Fileinfo",
  props: ["type", "formId", "isview"],
  mixins: [editForm],
  components: {
    UploadFile,
  },
  data() {
    return {
      formItemLayout: {
        labelCol: { span: 3 },
      },
      path: {},
      file_data: {
        ebcn: "com.cat.project.model.Schedule",
        sign: "A1",
        keyValue: this.formId,
      },
    };
  },
  created() {
     this.spinning = false;
  },
  methods: {},
};
</script>

<style>
.ant-advanced-search-form {
  padding: 24px;
  border-radius: 6px;
}

.ant-advanced-search-form .ant-form-item {
  display: flex;
}

.ant-advanced-search-form .ant-form-item-control-wrapper {
  flex: 1;
}
</style>