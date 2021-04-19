<template>
  <a-spin :spinning="spinning">
    <a-form class="ant-advanced-search-form">
      <a-row :gutter="50">
        <a-col :span="24">
          <a-form-item v-bind="formItemLayout" label="个人照片">
            <upload-img
              :multiple="false"
              :file_data="file_data"
              :length="isview ? -1 : 1"
            ></upload-img>
          </a-form-item>
        </a-col>
      </a-row>
      <a-row :gutter="50">
        <a-col :span="24">
          <a-form-item v-bind="formItemLayout" label="身份证附件">
            <upload-img
              :multiple="false"
              :file_data="file_data_certcart"
              :length="isview ? -1 : 2"
            ></upload-img>
          </a-form-item>
        </a-col>
      </a-row>
      <a-row :gutter="50">
        <a-col :span="24">
          <a-form-item v-bind="formItemLayout" label="学历证书附件">
            <upload-img
              :multiple="true"
              :file_data="file_data_educart"
              :length="isview ? -1 : 100"
            ></upload-img>
          </a-form-item>
        </a-col>
      </a-row>
      <a-row :gutter="50">
        <a-col :span="24">
          <a-form-item v-bind="formItemLayout" label="劳动合同附件">
            <upload-file
              :multiple="true"
              :file_data="file_data_contract"
              :length="-1"
              :key="file_data_contract.keyValues[0]"
            ></upload-file>
          </a-form-item>
        </a-col>
      </a-row>
      <a-row :gutter="50">
        <a-col :span="24">
          <a-form-item v-bind="formItemLayout" label="个人证书附件">
            <upload-img
              :multiple="true"
              :file_data="file_data_certificate"
              :length="-1"
              :key="file_data_certificate.keyValues[0]"
            ></upload-img>
          </a-form-item>
        </a-col> </a-row
      ><a-row :gutter="50">
        <a-col :span="24">
          <a-form-item v-bind="formItemLayout" label="其他个人附件">
            <upload-file
              :multiple="true"
              :file_data="file_data_other"
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
import { queryService } from "@/api/manage";
import UploadFile from "@/components/File/UploadFile";
import UploadImg from "@/components/File/UploadImg";
import axios from "axios";

export default {
  name: "Form_Fileinfo",
  props: ["type", "id", "isview"],
  mixins: [editForm],
  components: {
    UploadFile,
    UploadImg,
  },
  data() {
    return {
      formItemLayout: {
        labelCol: { span: 3 },
      },
      path: {},
      file_data: {
        ebcn: "com.cat.system.model.Person",
        sign: "A1",
        keyValue: this.id,
      },
      file_data_certcart: {
        ebcn: "com.cat.system.model.Person",
        sign: "A2",
        keyValue: this.id,
      },
      file_data_educart: {
        ebcn: "com.cat.system.model.Person",
        sign: "A3",
        keyValue: this.id,
      },
      file_data_contract: {
        ebcn: "com.cat.personnel.model.LaborContract",
        sign: "A1",
        keyValues: [],
      },
      file_data_certificate: {
        ebcn: "com.cat.personnel.model.Certificate",
        sign: "A1",
        keyValues: [],
      },
      file_data_other: {
        ebcn: "com.cat.system.model.Person",
        sign: "A4",
        keyValue: this.id,
      },
    };
  },
  async created() {
    this.spinning = true;
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.contract = prefix + "/postinformation/query_laborcontract";
    this.path.certificate = prefix + "/postinformation/query_certificate";
    const that = this;
    await axios
      .all([
        queryService(that.path.contract, {
          person_id: that.id,
        }),
        queryService(that.path.certificate, {
          person_id: that.id,
        }),
      ])
      .then(
        axios.spread(function (accts, perms) {
          if (accts.code === "400" || perms.code === "400") {
            that.$notification.error({
              message: "参数错误",
              description: "请求出现错误，请联系管理员",
            });
          } else {
            let l = accts.result.map((acct) => {
              return acct.id;
            });
            that.file_data_contract.keyValues = l;
            let m = perms.result.map((perm) => {
              return perm.id;
            });
            that.file_data_certificate.keyValues = m;
          }
          that.spinning = false;
        })
      );
    console.log("file_data_contract");
    console.log(that.file_data_contract);
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
