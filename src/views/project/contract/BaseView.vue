<template>
  <a-spin :spinning="spinning" style="margin-top: 15px; margin-left: 15px">
    <a-row class="form-row" :gutter="16">
      <a-col>
        <a-descriptions bordered :column="2">
          <a-descriptions-item label="销售合同单号">{{
            data.coding
          }}</a-descriptions-item>
          <a-descriptions-item label="关联跟单号">{{
            data.xmgd_coding
          }}</a-descriptions-item>
          <a-descriptions-item label="项目名称">{{
            data.projectName
          }}</a-descriptions-item>
          <a-descriptions-item label="合同甲方全称">{{
            data.partyaName
          }}</a-descriptions-item>
          <a-descriptions-item label="销售合同金额">{{
            data.money
          }}</a-descriptions-item>
          <a-descriptions-item label="项目来源">{{
            data.source_name
          }}</a-descriptions-item>
          <a-descriptions-item label="合同质保期">{{
            data.warranty
          }}</a-descriptions-item>
          <a-descriptions-item label="合同收款次数与金额">{{
            data.norm
          }}</a-descriptions-item>
          <a-descriptions-item label="最后一次收款时间">{{
            data.lastskdate
          }}</a-descriptions-item>
          <a-descriptions-item label="提交人">{{
            data.userName
          }}</a-descriptions-item>
          <a-descriptions-item label="提交时间">{{
            data.ct
          }}</a-descriptions-item>
          <a-descriptions-item label="合同扫描状态">
            <div :style="productState(data.htsmState_code)">
              {{ data.htsmState_name }}
            </div>
          </a-descriptions-item>
          <a-descriptions-item label="开票状态">
            <div :style="productState(data.kpState_code)">
              {{ data.kpState_name }}
            </div>
          </a-descriptions-item>
          <a-descriptions-item label="收款状态">
            <div :style="productState(data.skState_code)">
              {{ data.skState_name }}
            </div>
          </a-descriptions-item>
          <a-descriptions-item label="发货状态">
            <div :style="productState(data.fhState_code)">
              {{ data.fhState_name }}
            </div>
          </a-descriptions-item>
          <a-descriptions-item label="售中支持状态">
            <div :style="productState(data.szzcState_code)">
              {{ data.szzcState_name }}
            </div>
          </a-descriptions-item>
          <a-descriptions-item label="合同结算状态">
            <div :style="productState(data.htjsState_code)">
              {{ data.htjsState_name }}
            </div>
          </a-descriptions-item>
        </a-descriptions>
      </a-col>
    </a-row>
    <a-row :gutter="50">
      <a-col :span="24">
        <a-form-item label="附件">
          <upload-file
            :multiple="true"
            :file_data="file_data_contract"
            :length="-1"
            :key="file_data_contract.keyValue"
          ></upload-file>
        </a-form-item> </a-col
    ></a-row>
    <a-row :gutter="50">
      <a-col :span="24">
        <a-form-item label="图片">
          <upload-img
            :multiple="true"
            :file_data="file_data_contract2"
            :length="-1"
            :key="file_data_contract2.keyValue"
          ></upload-img>
        </a-form-item> </a-col
    ></a-row>
  </a-spin>
</template>
<script>
import { back_to_list } from "@/api/manage";
import UploadFile from "@/components/File/UploadFile";
import UploadImg from "@/components/File/UploadImg";

export default {
  name: "Schedule_Form",
  props: ["formId", "type", "base", "XMSTATE"],
  components: {
    UploadFile,
    UploadImg,
  },
  data() {
    return {
      form: this.$form.createForm(this),
      spinning: true,
      memberLoading: false,
      path: {},
      data: {},
      formItemLayout: {
        labelCol: { span: 3 },
      },
      file_data_contract: {
        ebcn: "com.cat.project.model.Contract",
        sign: "A1",
        keyValue: this.formId,
      },
      file_data_contract2: {
        ebcn: "com.cat.project.model.Contract",
        sign: "A2",
        keyValue: this.formId,
      },
    };
  },
  mounted() {
    this.data = this.base;
    this.spinning = false;
  },
  methods: {
    productState(stateCode) {
      var color = "";
      this.XMSTATE.forEach((item) => {
        if (stateCode == item.code) {
          color = "color:" + item.colorCode;
        }
      });
      return color;
    },
    back() {
      back_to_list(this);
    },
  },
};
</script>
