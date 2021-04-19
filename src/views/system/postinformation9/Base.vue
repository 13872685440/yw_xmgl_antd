<template>
  <a-spin :spinning="spinning">
    <a-form :form="form" class="form" @submit="handleSubmit">
      <a-row class="form-row" :gutter="16">
        <a-col :xl="{ span: 16 }" :lg="{ span: 16 }" :md="{ span: 16 }" :sm="24">
          <a-row>
            <a-col :xl="{ span: 11 }" :lg="{ span: 12 }" :md="{ span: 12 }" :sm="24">
              <a-form-item label="所在部门">
                <a-input
                  :readOnly="true"
                  v-decorator="[
                    'organ_name',
                    {
                      rules: [
                        {
                          required: true,
                          message: '请输入所在部门',
                          whitespace: true,
                        },
                      ],
                    },
                  ]"
                />
                <a-input hidden v-decorator="['organ_id']" />
                <a-input hidden v-decorator="['id']" />
              </a-form-item> </a-col
            ><a-col
              :xl="{ span: 11, offset: 1 }"
              :lg="{ span: 12 }"
              :md="{ span: 12 }"
              :sm="24"
            >
              <a-form-item label="员工编号">
                <a-input
                  @blur.prevent="onchange_unique"
                  v-decorator="[
                    'loginName',
                    {
                      rules: [
                        {
                          required: true,
                          message: '请输入员工编号',
                          whitespace: true,
                        },
                      ],
                    },
                  ]"
                />
              </a-form-item>
            </a-col>
          </a-row>
          <a-row>
            <a-col :xl="{ span: 11 }" :lg="{ span: 12 }" :md="{ span: 12 }" :sm="24">
              <a-form-item label="姓名">
                <a-input
                  v-decorator="[
                    'person_name',
                    {
                      rules: [
                        {
                          required: true,
                          message: '请输入姓名',
                          whitespace: true,
                        },
                      ],
                    },
                  ]"
                />
                <a-input hidden v-decorator="['person_id']" />
              </a-form-item>
            </a-col>
            <a-col
              :xl="{ span: 11, offset: 1 }"
              :lg="{ span: 12 }"
              :md="{ span: 12 }"
              :sm="24"
              ><a-form-item label="身份证号">
                <a-input
                  @blur.prevent="onchange"
                  v-decorator="[
                    'certnum',
                    {
                      rules: [
                        {
                          required: true,
                          message: '请输入身份证号',
                          whitespace: true,
                        },
                      ],
                    },
                  ]"
                />
              </a-form-item> </a-col
          ></a-row>
          <a-row>
            <a-col :xl="{ span: 11 }" :lg="{ span: 12 }" :md="{ span: 12 }" :sm="24"
              ><a-form-item label="证件有效期">
                <a-date-picker
                  style="width: 100%"
                  format="YYYY-MM-DD"
                  v-decorator="[
                    'expiredate',
                    { rules: [{ required: true, message: '请选择证件有效期' }] },
                  ]"
                />
              </a-form-item>
            </a-col>
            <a-col
              :xl="{ span: 11, offset: 1 }"
              :lg="{ span: 12 }"
              :md="{ span: 12 }"
              :sm="24"
            >
              <a-form-item label="手机号码">
                <a-input
                  @blur.prevent="onchange_unique"
                  v-decorator="[
                    'phone',
                    {
                      rules: [
                        {
                          required: true,
                          message: '请输入手机号码',
                          whitespace: true,
                        },
                      ],
                    },
                  ]"
                />
              </a-form-item> </a-col></a-row
        ></a-col>
        <a-col :xl="{ span: 7 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24">
          <img
            :src="photo_url"
            v-if="photo_url != ''"
            alt=""
            style="width: 150px; height: 200px; margin-top: 10px" /></a-col></a-row
      ><a-row class="form-row" :gutter="16">
        <a-col :xl="{ span: 7 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24"
          ><a-form-item label="出生年月">
            <a-input :readOnly="true" v-decorator="['birthday']" />
          </a-form-item>
        </a-col>
        <a-col :xl="{ span: 7, offset: 1 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24"
          ><a-form-item label="性别">
            <a-input :readOnly="true" v-decorator="['sex']" />
          </a-form-item>
        </a-col>
        <a-col :xl="{ span: 7, offset: 1 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24"
          ><a-form-item label="民族">
            <a-select
              placeholder="请选择民族"
              showSearch
              v-decorator="[
                'race_code',
                {
                  rules: [
                    {
                      required: true,
                      message: '请选择民族',
                      whitespace: true,
                    },
                  ],
                },
              ]"
            >
              <a-select-option
                v-for="type in RACES"
                :key="type.code"
                :value="type.code"
                >{{ type.name }}</a-select-option
              >
            </a-select>
          </a-form-item>
        </a-col>
      </a-row>
      <a-row class="form-row" :gutter="16">
        <a-col :xl="{ span: 7 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24"
          ><a-form-item label="入职时间">
            <a-date-picker
              style="width: 100%"
              format="YYYY-MM-DD"
              v-decorator="[
                'entrytime',
                {
                  rules: [
                    {
                      required: true,
                      message: '请选择入职时间',
                    },
                  ],
                },
              ]"
            />
          </a-form-item>
        </a-col>
        <a-col :xl="{ span: 7, offset: 1 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24">
          <a-form-item label="员工状态">
            <a-select
              placeholder="请选择员工状态"
              v-decorator="[
                'poststate_code',
                {
                  rules: [
                    {
                      required: true,
                      message: '请选择员工状态',
                      whitespace: true,
                    },
                  ],
                },
              ]"
            >
              <a-select-option
                v-for="type in POSTSTATES"
                :key="type.code"
                :value="type.code"
                >{{ type.name }}</a-select-option
              >
            </a-select>
          </a-form-item>
        </a-col>
        <a-col :xl="{ span: 3, offset: 1 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24">
          <a-form-item label="试用期">
            <a-input-number
              style="width: 60px"
              v-decorator="[
                'probation',
                {
                  rules: [
                    {
                      type: 'number',
                      message: '请输入数字',
                    },
                  ],
                },
              ]"
            />&#12288; 个月
          </a-form-item> </a-col
        ><a-col :xl="{ span: 3, offset: 1 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24"
          ><a-form-item label="转正考核分数">
            <a-input-number
              v-decorator="[
                'checkpoint',
                {
                  rules: [
                    {
                      type: 'number',
                      message: '请输入数字',
                    },
                  ],
                },
              ]"
              :min="0"
              :max="100"
              :step="0.01"
            />
          </a-form-item>
        </a-col>
      </a-row>
      <a-row class="form-row" :gutter="16">
        <a-col :xl="{ span: 15 }" :lg="{ span: 15 }" :md="{ span: 15 }" :sm="24"
          ><a-form-item label="身份证地址">
            <a-input
              v-decorator="[
                'regisplace',
                {
                  rules: [
                    {
                      required: true,
                      message: '请输入身份证地址',
                      whitespace: true,
                    },
                  ],
                },
              ]"
            />
          </a-form-item> </a-col
        ><a-col
          :xl="{ span: 7, offset: 1 }"
          :lg="{ span: 8 }"
          :md="{ span: 12 }"
          :sm="24"
        >
          <a-form-item label="转正时间">
            <a-date-picker
              style="width: 100%"
              format="YYYY-MM-DD"
              v-decorator="['regulartime']"
            />
          </a-form-item>
        </a-col>
      </a-row>
      <a-row class="form-row" :gutter="16">
        <a-col :xl="{ span: 15 }" :lg="{ span: 15 }" :md="{ span: 15 }" :sm="24"
          ><a-form-item label="居住地址">
            <a-input
              v-decorator="[
                'address',
                {
                  rules: [
                    {
                      required: true,
                      message: '请输入居住地址',
                      whitespace: true,
                    },
                  ],
                },
              ]"
            />
          </a-form-item> </a-col
        ><a-col :xl="{ span: 7, offset: 1 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24"
          ><a-form-item label="婚姻状况">
            <a-select
              placeholder="请选择婚姻状况"
              v-decorator="[
                'maritalstatus_code',
                {
                  rules: [
                    {
                      required: true,
                      message: '请选择婚姻状况',
                      whitespace: true,
                    },
                  ],
                },
              ]"
            >
              <a-select-option
                v-for="type in MARITALS"
                :key="type.code"
                :value="type.code"
                >{{ type.name }}</a-select-option
              >
            </a-select>
          </a-form-item>
        </a-col>
      </a-row>
      <a-row class="form-row" :gutter="16">
        <a-col :xl="{ span: 7 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24"
          ><a-form-item label="学历">
            <a-select
              placeholder="请选择学历"
              v-decorator="[
                'education_code',
                {
                  rules: [
                    {
                      required: true,
                      message: '请选择学历',
                      whitespace: true,
                    },
                  ],
                },
              ]"
            >
              <a-select-option
                v-for="type in EDUCATIONS"
                :key="type.code"
                :value="type.code"
                >{{ type.name }}</a-select-option
              >
            </a-select>
          </a-form-item> </a-col
        ><a-col :xl="{ span: 7, offset: 1 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24"
          ><a-form-item label="毕业院校">
            <a-input
              v-decorator="[
                'school',
                {
                  rules: [
                    {
                      required: true,
                      message: '请输入毕业院校',
                      whitespace: true,
                    },
                  ],
                },
              ]"
            />
          </a-form-item> </a-col
        ><a-col :xl="{ span: 7, offset: 1 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24"
          ><a-form-item label="毕业时间">
            <a-date-picker
              style="width: 100%"
              format="YYYY-MM-DD"
              v-decorator="[
                'graduatetime',
                {
                  rules: [
                    {
                      required: true,
                      message: '请选择毕业时间',
                    },
                  ],
                },
              ]"
            />
          </a-form-item>
        </a-col>
      </a-row>
      <a-row class="form-row" :gutter="16">
        <a-col :xl="{ span: 7 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24"
          ><a-form-item label="初次工作日期">
            <a-date-picker
              style="width: 100%"
              format="YYYY-MM-DD"
              v-decorator="[
                'fristday',
                {
                  rules: [
                    {
                      required: true,
                      message: '请选择初次工作日期',
                    },
                  ],
                },
              ]"
            />
          </a-form-item> </a-col
        ><a-col :xl="{ span: 7, offset: 1 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24"
          ><a-form-item label="专业">
            <a-input
              v-decorator="[
                'major',
                {
                  rules: [
                    {
                      required: true,
                      message: '请输入专业',
                      whitespace: true,
                    },
                  ],
                },
              ]"
            />
          </a-form-item> </a-col
        ><a-col :xl="{ span: 7, offset: 1 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24"
          ><a-form-item label="开始任职店长时间"
            ><a-date-picker
              style="width: 100%"
              format="YYYY-MM-DD"
              v-decorator="['directortime']"
            />
          </a-form-item> </a-col
      ></a-row>
      <a-row class="form-row" :gutter="16">
        <a-col :xl="{ span: 7 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24"
          ><a-form-item label="任职状态"
            ><a-select
              placeholder="请选择任职状态"
              v-decorator="[
                'leaf_code',
                {
                  rules: [
                    {
                      required: true,
                      message: '请选择任职状态',
                      whitespace: true,
                    },
                  ],
                },
              ]"
            >
              <a-select-option
                v-for="type in POSTTYPE"
                :key="type.code"
                :value="type.code"
                >{{ type.name }}</a-select-option
              >
            </a-select></a-form-item
          ></a-col
        ><a-col :xl="{ span: 7, offset: 1 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24"
          ><a-form-item label="银行卡号">
            <a-input
              v-decorator="[
                'cardno',
                {
                  rules: [
                    {
                      required: true,
                      message: '请输入银行卡号',
                      whitespace: true,
                    },
                  ],
                },
              ]"
            />
          </a-form-item> </a-col
        ><a-col :xl="{ span: 7, offset: 1 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24"
          ><a-form-item label="开户行">
            <a-input
              v-decorator="[
                'openbank',
                {
                  rules: [
                    {
                      required: true,
                      message: '请输入开户行',
                      whitespace: true,
                    },
                  ],
                },
              ]"
            />
          </a-form-item> </a-col></a-row
      ><a-row class="form-row" :gutter="16">
        <a-col :xl="{ span: 7 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24"
          ><a-form-item label="紧急联系人">
            <a-input
              v-decorator="[
                'emergencyname',
                {
                  rules: [
                    {
                      required: true,
                      message: '请输入紧急联系人',
                      whitespace: true,
                    },
                  ],
                },
              ]"
            /> </a-form-item></a-col
        ><a-col :xl="{ span: 7, offset: 1 }" :lg="{ span: 8 }" :md="{ span: 12 }" :sm="24"
          ><a-form-item label="紧急联系人电话">
            <a-input
              v-decorator="[
                'emergencyphone',
                {
                  rules: [
                    {
                      required: true,
                      message: '请输入紧急联系人电话',
                      whitespace: true,
                    },
                  ],
                },
              ]"
            />
          </a-form-item> </a-col
        ><a-col
          :xl="{ span: 7, offset: 1 }"
          :lg="{ span: 8 }"
          :md="{ span: 12 }"
          :sm="24"
        >
          <a-form-item v-if="formId != ''" label="重置密码">
            <a-input :readOnly="true" v-decorator="['pwd']">
              <a-icon
                slot="addonAfter"
                type="reload"
                style="color: red; cursor: pointer"
                @click="reset()"
              />
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
import moment from "moment";
import { ACCESS_TOKEN } from "@/store/mutation-types";
import Vue from "vue";

const savePath = "/" + MODEL_SYSTEM + "/postinformation/save";
const getCertNumPath = "/" + MODEL_SYSTEM + "/postinformation/validationCertNum";
const getUniquePath = "/" + MODEL_SYSTEM + "/postinformation/validationUnique";
const resetPath = "/" + MODEL_SYSTEM + "/postinformation/reset";
const hasfilePath = "/" + MODEL_SYSTEM + "/file/hasfile";
const photoPath = "/api/" + MODEL_SYSTEM + "/file/viewimage?id=";

export default {
  name: "Post_Base_Form",
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
