<template>
  <div>
    <s-list v-if="currentTab === 'list'" :searchCode="searchCode"  @edit="edit" @search="search"/>
    <s-form v-if="currentTab === 'add'"  :formId="id" :type="type" :LEAVETTYPE="LEAVETTYPE"  @edit="edit"/>
    <s-view v-if="currentTab === 'view'" :formId="id" :type="type"  @edit="edit" />

  </div>
</template>

<script>
import SList from "./List";
import SForm from "./Form";
import SView from "./View";

import { queryService } from "@/api/manage";
import { MODEL_SYSTEM_DICTIONARY } from "@/store/mutation-types";


export default {
  name: "Main_leave",
  components: {
    SList,
    SForm,
    SView,
  },
  data() {
    return {
      currentTab: "list",
      id: "",
      type: "",
      searchCode: "",
      LEAVETTYPE:[],
    };
  },
   created() {
    queryService(MODEL_SYSTEM_DICTIONARY, {typeCode: "LEAVETTYPE"}).then((res) => {
      if (res.code === "400") {
        this.$notification.error({
          message: "参数错误",
          description: "请求出现错误，请联系管理员",
        });
      } else {
        this.LEAVETTYPE = res.result;
      }
      this.spinning = false;
    });
  },
  methods: {
    edit(data) {
      this.id = data.id;
      this.currentTab = data.state;
      this.type = data.type;
    },
    search(value) {
      this.searchCode = value;
    }
  }
};
</script>
