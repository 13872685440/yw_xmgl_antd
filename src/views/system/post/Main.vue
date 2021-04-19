<template>
  <div>
    <s-list v-if="currentTab === 'list'" :searchCode="searchCode" @edit="edit" @search="search" />
    <s-form v-if="currentTab === 'add'" :formId="id" :type="type" @edit="edit" :ORGS="ORGS" :ROLES="ROLES"/>
  </div>
</template>

<script>
import SList from "./List";
import SForm from "./Form";
import { queryService } from "@/api/manage";
import {MODEL_SYSTEM  } from "@/store/mutation-types";

export default {
  name: "Main_Post",
  components: {
    SList,
    SForm,
  },

  data() {
    return {
      currentTab: "list",
      id: "",
      type: "",
      searchCode: "",
      ORGS: [],
      ROLES:[],
      path:{}
    };
  },
  created() {
    this.path.org_path = "/" + MODEL_SYSTEM + "/organization/get";
    console.log(this.path.org_path);
    queryService(this.path.org_path,{}).then((res) => {
      if (res.code === "400") {
        this.$notification.error({
          message: "参数错误",
          description: "请求出现错误，请联系管理员",
        });
      } else {
        this.ORGS = res.result;
      }
    });
    this.path.role_path = "/" + MODEL_SYSTEM + "/role/get";
    console.log(this.path.role_path);
    queryService(this.path.role_path,{rtype:"RT002"}).then((res) => {
      if (res.code === "400") {
        this.$notification.error({
          message: "参数错误",
          description: "请求出现错误，请联系管理员",
        });
      } else {
        this.ROLES = res.result;
      }
    });
  },
  methods: {
    edit(data) {
      this.currentTab = data.state;
      this.id = data.id;
      this.type = data.type;
    },
    search(value) {
      this.searchCode = value;
    },
  },
};
</script>
