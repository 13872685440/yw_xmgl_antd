<template>
  <a-spin :spinning="spinning">
    <a-card
      :body-style="{ padding: '24px 32px' }"
      :bordered="false"
      class="card"
    >
      <a-form :form="form" class="form" @submit="handleSubmit">
        <a-row class="form-row" :gutter="16">
          <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="销售合同单号">
              <a-input
                @blur.prevent="onchange_unique"
                v-decorator="[
                  'coding',
                  {
                    rules: [
                      {
                        required: true,
                        message: '请输入销售合同单号',
                        whitespace: true,
                      },
                    ],
                  },
                ]"
              />
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="关联跟单号">
              <a-select
                v-if="base.xmgd_id == undefined"
                show-search
                show-arrow
                placeholder="请输入关键词"
                style="width: 100%"
                :value="xmgd_id"
                :default-active-first-option="false"
                :filter-option="false"
                :not-found-content="null"
                @search="handleSearch"
                @change="handleChange"
              >
                <a-icon slot="suffixIcon" type="close" @click="onck" />
                <a-select-option v-for="d in gdhData" :key="d.value">{{
                  d.text
                }}</a-select-option>
              </a-select>
              <a-input hidden v-decorator="['xmgd_id']" />
              <a-input hidden v-decorator="['xmgd_coding']" />
              <a-input
                v-if="base.xmgd_id != undefined"
                :disabled="true"
                v-decorator="['xmgd_coding']"
              />
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="项目名称">
              <a-input
                v-decorator="[
                  'projectName',
                  {
                    rules: [
                      {
                        required: true,
                        message: '请输入项目名称',
                        whitespace: true,
                      },
                    ],
                  },
                ]"
              />
            </a-form-item>
          </a-col>
          <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="合同甲方全称">
              <a-input
                v-decorator="[
                  'partyaName',
                  {
                    rules: [
                      {
                        required: true,
                        message: '请输入合同甲方全称',
                        whitespace: true,
                      },
                    ],
                  },
                ]"
              />
            </a-form-item>
          </a-col>

          <a-col :xl="12" :lg="24" :md="24" :sm="24">
            <a-form-item label="销售合同金额">
              <a-input v-decorator="['money', validateForm.money]" />
            </a-form-item>
          </a-col>
          <a-col
            :xl="{ span: 12 }"
            :lg="{ span: 12 }"
            :md="{ span: 12 }"
            :sm="24"
          >
            <a-form-item label="项目来源">
              <a-select
                placeholder="请选择项目来源"
                v-decorator="[
                  'source_code',
                  {
                    rules: [
                      {
                        required: true,
                        message: '请选择项目来源',
                        whitespace: true,
                      },
                    ],
                  },
                ]"
              >
                <a-select-option
                  v-for="source in SOURCE"
                  :key="source.code"
                  :value="source.code"
                  >{{ source.name }}</a-select-option
                >
              </a-select>
            </a-form-item>
          </a-col>
          <a-col
            :xl="{ span: 12 }"
            :lg="{ span: 12 }"
            :md="{ span: 12 }"
            :sm="24"
          >
            <a-form-item label="合同质保期">
              <a-date-picker
                format="YYYY-MM-DD"
                v-decorator="[
                  'warranty',
                  {
                    rules: [
                      {
                        type: 'object',
                        required: true,
                        message: '请选择日期!',
                        whitespace: true,
                      },
                    ],
                  },
                ]"
                style="width: 100%"
              />
            </a-form-item>
          </a-col>
          <a-col
            :xl="{ span: 12 }"
            :lg="{ span: 12 }"
            :md="{ span: 12 }"
            :sm="24"
          >
            <a-form-item label="合同收款次数与金额">
              <a-input
                v-decorator="[
                  'norm',
                  {
                    rules: [
                      {
                        required: true,
                        message: '请输入合同收款次数与金额',
                        whitespace: true,
                      },
                    ],
                  },
                ]"
              />
            </a-form-item>
          </a-col>
          <a-col
            :xl="{ span: 12 }"
            :lg="{ span: 12 }"
            :md="{ span: 12 }"
            :sm="24"
          >
            <a-form-item label="最后一次收款时间">
              <a-date-picker
                format="YYYY-MM-DD"
                v-decorator="[
                  'lastskdate',
                  {
                    rules: [
                      {
                        type: 'object',
                        required: true,
                        message: '请选择日期',
                        whitespace: true,
                      },
                    ],
                  },
                ]"
                style="width: 100%"
              />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="50">
          <a-col :span="24">
            <a-form-item label="附件">
              <upload-file
                :multiple="true"
                :file_data="file_data"
                :key="key"
              ></upload-file>
            </a-form-item>
          </a-col> </a-row
        ><a-row :gutter="50">
          <a-col :span="24">
            <a-form-item label="图片">
              <upload-img
                :multiple="true"
                :file_data="file_data2"
                :key="key"
              ></upload-img>
            </a-form-item>
          </a-col>
        </a-row>
        <a-form-item :wrapperCol="{ span: 24 }" style="text-align: center">
          <a-button htmlType="submit" type="primary">保存</a-button>
        </a-form-item>
      </a-form>
    </a-card>
  </a-spin>
</template>
<script>
import { back_to_list, saveService, queryService } from "@/api/manage";
import { MODEL_SYSTEM } from "@/store/mutation-types";
import UploadFile from "@/components/File/UploadFile";
import UploadImg from "@/components/File/UploadImg";
import moment from "moment";

const savePath = "/" + MODEL_SYSTEM + "/contract/save";
const getXmgdPath = "/" + MODEL_SYSTEM + "/schedule/getListByCoding";
const findPath = "/" + MODEL_SYSTEM + "/schedule/find";
const getUniquePath = "/" + MODEL_SYSTEM + "/contract/validationUnique";

export default {
  name: "Contract_Form",
  props: ["formId", "type", "base", "SOURCE"],
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
      data: [],
      gdhData: [],
      xmgd_id: undefined,
      errordata: {},
      formItemLayout: {
        labelCol: { span: 3 },
      },
      file_data: {
        ebcn: "com.cat.project.model.Contract",
        sign: "A1",
      },
      file_data2: {
        ebcn: "com.cat.project.model.Contract",
        sign: "A2",
      },
      key: "",
      validateForm: {
        money: {
          // 金额   必填 数字 限制大小  小数点后只能两位小数
          rules: [
            {
              required: true,
              validator: (rule, value, cbfn) => {
                value = value.toString();
                if (!value) {
                  cbfn("请输入金额");
                } else if (!value.trim().length) {
                  cbfn("请输入金额");
                } else if (isNaN(value)) {
                  cbfn("请输入正确的金额");
                } else if (Number(value) > Number(this.avalible)) {
                  cbfn("金额已超出可用金额");
                } else if (
                  value.indexOf(".") > -1 &&
                  value.toString().split(".")[1].length > 2
                ) {
                  cbfn("请输入正确的金额，小数点后保留两位数字");
                }
                cbfn();
              },
            },
          ],
        },
      },
    };
  },
  mounted() {
    if (this.formId != null) {
      this.file_data.keyValue = this.formId;
      this.file_data2.keyValue = this.formId;
      this.key = this.formId;
    } else {
      const random = Math.random().toString(36).substring(2);
      this.key = random;
      this.file_data.keyValue = random;
      this.file_data2.keyValue = random;
    }
    this.base.money =
      this.base.money != undefined ? Number(this.base.money) : "";
    this.form.setFieldsValue(this.base);
    this.form.setFieldsValue({
      warranty: moment(this.base.warranty),
      lastskdate: moment(this.base.lastskdate),
    });
    this.spinning = false;
  },
  methods: {
    onchange_unique(e) {
      const { id, value } = e.target;
      var error = "销售合同单号已存在";
      const data = {
        key: id,
        value: value,
        error: error,
      };
      this.memberLoading = true;
      this.errordata = {};
      queryService(getUniquePath, data).then((res) => {
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
    onck() {
      this.xmgd_id = undefined;
    },
    //输入框事件
    handleSearch(keyword) {
      var that = this;
      this.xmgd_id = undefined;
      queryService(getXmgdPath, { keyword: keyword }).then((res) => {
        that.gdhData = [];
        res.result.forEach((value) => {
          that.gdhData.push({
            value: value.id,
            text: value.coding,
          });
        });
      });
    },
    handleChange(value) {
      queryService(findPath, { id: value }).then((res) => {
        this.base.projectName = res.result.projectName;
        this.base.source_code = res.result.source_code;
        this.base.source_name = res.result.source_name;
        this.form.setFieldsValue({
          xmgd_coding: res.result.coding,
          xmgd_id: res.result.id,
        });
      });
      this.xmgd_id = value;
    },
    handleSubmit(e) {
      e.preventDefault();
      var that = this;
      that.form.validateFields((err, values) => {
        if (!err) {
          if (that.errordata.coding != undefined) {
            that.form.setFields(that.errordata);
          } else {
            values.id = this.formId;
            values.tmpId = this.key;
            values.warranty = moment(values.warranty).format("YYYY-MM-DD");
            values.lastskdate = moment(values.lastskdate).format("YYYY-MM-DD");
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
          }
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
