<template>
  <span>
    <a-card>
      <span slot="title" class="card">
        <div>{{ base.person_name }}</div>
        <div style="color: rgba(0, 0, 0, 0.45); font-size: 14px; padding-top: 5px">
          <span>{{ base.organ_name }}</span> <a-divider type="vertical" /><span
            v-if="base.post_names"
          >
            <template v-for="name in base.post_names">
              {{ name }}
            </template>
          </span>
        </div>
      </span>
      <a-row class="form-row" :gutter="16"
        ><a-col :xl="{ span: 16 }" :lg="{ span: 16 }" :md="{ span: 16 }" :sm="24">
          <detail-list size="small" :col="2" class="detail-layout">
            <detail-list-item term="员工编号">{{ base.loginName }}</detail-list-item>
            <detail-list-item term="手机号码">{{ base.phone }}</detail-list-item>
            <detail-list-item term="身份证号">{{ base.certnum }}</detail-list-item>
            <detail-list-item term="证件有效期">{{ base.expiredate }}</detail-list-item>
            <detail-list-item term="出生日期">{{ base.birthday }}</detail-list-item>
            <detail-list-item term="性别">{{ base.sex }}</detail-list-item>
            <detail-list-item term="婚姻状况">{{
              base.maritalstatus_name
            }}</detail-list-item>
            <detail-list-item term="民族">{{ base.race_name }}</detail-list-item>
          </detail-list></a-col
        ><a-col :xl="{ span: 7 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24">
          <img
            :src="photo_url"
            v-if="photo_url != ''"
            alt=""
            style="width: 105px; height: 140px" /></a-col
      ></a-row>
      <detail-list size="small" :col="1" class="detail-layout">
        <detail-list-item term="身份证地址">{{ base.regisplace }}</detail-list-item>
        <detail-list-item term="居住地址">{{
          base.address
        }}</detail-list-item> </detail-list
      ><detail-list size="small" :col="3" class="detail-layout">
        <detail-list-item term="学历">{{ base.education_name }}</detail-list-item>
        <detail-list-item term="毕业院校">{{ base.school }}</detail-list-item>
        <detail-list-item term="毕业时间">{{
          base.graduatetime
        }}</detail-list-item></detail-list
      >
      <detail-list size="small" :col="3" class="detail-layout">
        <detail-list-item term="开户行">{{ base.openbank }}</detail-list-item>
        <detail-list-item term="专业">{{ base.major }}</detail-list-item>
        <detail-list-item term="初次工作时间">{{ base.fristday }}</detail-list-item>
        <detail-list-item term="银行账号">{{ base.cardno }}</detail-list-item>
        <detail-list-item term="紧急联系人">{{ base.emergencyname }}</detail-list-item>
        <detail-list-item term="紧急联系电话">{{ base.emergencyphone }}</detail-list-item>
      </detail-list> </a-card
    ><a-card title="任职信息" style="margin-top: 10px"
      ><detail-list size="small" :col="3" class="detail-layout">
        <detail-list-item term="岗位职级">{{ base.postlevel_code }}</detail-list-item>
        <detail-list-item term="基本工资">{{ base.postlevel_data }}</detail-list-item>
        <detail-list-item term="任职状态">{{ base.leaf_data }}</detail-list-item>
        <detail-list-item term="入职时间">{{ base.entrytime }}</detail-list-item>
        <detail-list-item term="员工状态">{{ base.poststate_name }}</detail-list-item>
        <detail-list-item term="初次任职店长时间">{{
          base.directortime
        }}</detail-list-item>
        <detail-list-item term="试用期">{{ base.probation }}</detail-list-item>
        <detail-list-item term="转正时间">{{ base.regulartime }}</detail-list-item>
        <detail-list-item term="转正考核分数">{{ base.checkpoint }}</detail-list-item>
      </detail-list>
    </a-card>
  </span>
</template>

<script>
import { queryService, toService, back_to_list, saveService } from "@/api/manage";
import { MODEL_SYSTEM } from "@/store/mutation-types";
import moment from "moment";
import { ACCESS_TOKEN } from "@/store/mutation-types";
import Vue from "vue";
import DetailList from "@/components/DescriptionList";

const DetailListItem = DetailList.Item;
const savePath = "/" + MODEL_SYSTEM + "/postinformation/save";
const getCertNumPath = "/" + MODEL_SYSTEM + "/postinformation/validationCertNum";
const getUniquePath = "/" + MODEL_SYSTEM + "/postinformation/validationUnique";
const resetPath = "/" + MODEL_SYSTEM + "/postinformation/reset";
const hasfilePath = "/" + MODEL_SYSTEM + "/file/hasfile";
const photoPath = "/api/" + MODEL_SYSTEM + "/file/viewimage?id=";

export default {
  name: "Post_Base_View",
  props: [
    "formId",
    "type",
    "POSTTYPE",
    "EDUCATIONS",
    "RACES",
    "MARITALS",
    "POSTSTATES",
    "base",
  ],
  components: {
    DetailList,
    DetailListItem,
  },
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
    if (this.base.id) {
      queryService(hasfilePath, {
        keyValue: this.base.person_id,
        ebcn: "com.cat.system.model.Person",
        sign: "A1",
      }).then((res) => {
        if (res.result) {
          this.photo_url = photoPath + res.result + "&token=" + Vue.ls.get(ACCESS_TOKEN);
        } else {
          this.photo_url = "";
        }
      });
      this.form.setFieldsValue({
        expiredate: moment(this.base.expiredate),
        entrytime: moment(this.base.entrytime),
        regulartime: moment(this.base.regulartime),
        graduatetime: moment(this.base.graduatetime),
        fristday: moment(this.base.fristday),
        directortime: moment(this.base.directortime),
      });
    }
    this.spinning = false;
  },
  methods: {
    handleSubmit(e) {
      e.preventDefault();
      this.form.validateFields((err, values) => {
        values.expiredate = moment(values.expiredate).format("YYYY-MM-DD");
        values.entrytime = moment(values.entrytime).format("YYYY-MM-DD");
        values.regulartime = moment(values.regulartime).format("YYYY-MM-DD");
        values.graduatetime = moment(values.graduatetime).format("YYYY-MM-DD");
        values.fristday = moment(values.fristday).format("YYYY-MM-DD");
        values.directortime = moment(values.directortime).format("YYYY-MM-DD");
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
    onchange(e) {
      const { value } = e.target;
      this.memberLoading = true;
      queryService(getCertNumPath, {
        id: this.base.person_id ? this.base.person_id : "xxxxxxxxxxxx",
        certnum: value,
      }).then((res) => {
        if (res.result && res.result.error) {
          this.form.setFields({
            certnum: {
              value: value,
              errors: [
                {
                  message: res.result.error,
                  field: "certnum",
                },
              ],
            },
          });
        } else {
          this.form.setFieldsValue({
            sex: res.result.sex,
            birthday: res.result.birthday,
          });
        }
        this.memberLoading = false;
      });
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
