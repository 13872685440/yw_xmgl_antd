<template>
  <a-spin :spinning="spinning">
    <a-form :form="form" class="form" @submit="handleSubmit">
      <a-row class="form-row" :gutter="16">
        <a-col :xl="{ span: 24 }" :lg="{ span: 24 }" :md="{ span: 24 }" :sm="24">
          <a-form-item label="任职岗位">
            <a-select
              @change="changePost"
              mode="multiple"
              size="default"
              placeholder="请选择岗位"
              style="width: 600px"
              v-decorator="[
                'posts',
                {
                  rules: [
                    {
                      required: true,
                      type: 'array',
                      message: '请选择岗位',
                      whitespace: true,
                    },
                  ],
                },
              ]"
            >
              <a-select-option v-for="type in POSTS" :key="type.id" :value="type.id">{{
                type.name
              }}</a-select-option>
            </a-select>
            <a-input hidden v-decorator="['id']" />
          </a-form-item>
        </a-col>
      </a-row>
      <a-row class="form-row" :gutter="16">
        <a-col :xl="{ span: 24 }" :lg="{ span: 24 }" :md="{ span: 24 }" :sm="24"
          ><a-form-item label="关联角色"
            ><a-checkbox-group disabled v-model="checkedList" :options="plainOptions" />
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
import { queryService, save } from "@/api/manage";
//const savePath = "/" + MODEL_SYSTEM + "/postinformation/save";

export default {
  name: "Post_PostRole_Form",
  props: ["formId", "type", "base"],
  data() {
    return {
      form: this.$form.createForm(this),
      spinning: true,
      memberLoading: false,
      data: [],
      path: {},
      POSTS: [],
      checkedList: [],
      plainOptions: [],
    };
  },
  mounted() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.getPost = prefix + "/post/get";
    this.path.getRole = prefix + "/post/getRole";
    this.path.save = prefix + "/postinformation/save2";
    queryService(this.path.getPost, { organ_id: this.base.organ_id }).then((res) => {
      this.POSTS = res.result;
    });
    this.form.setFieldsValue(this.base);
    console.log(this.base.posts)
    if(this.base.posts){
      this.changePost(this.base.posts);
    }
    this.spinning = false;
  },
  methods: {
    handleSubmit(e) {
      e.preventDefault();
      save(this, this.path.save);
      this.spinning = false;
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
  },
};
</script>

<style lang="less" scoped>
.card {
  margin-bottom: 24px;
}
</style>
