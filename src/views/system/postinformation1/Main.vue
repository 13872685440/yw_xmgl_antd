<template>
  <div>
    <s-list v-if="currentTab === 'list'" :searchCode="searchCode" @edit="edit" @search="search" />
    <s-form v-if="currentTab === 'add'" :formId="id" :type="type" @edit="edit" :POSTTYPE="POSTTYPE"/>
  </div>
</template>

<script>
import SList from "./List";
import SForm from "./Form";
import { queryService } from "@/api/manage";
import { MODEL_SYSTEM_DICTIONARY } from "@/store/mutation-types";

export default {
  name: "Main_Postinformation",
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
      POSTTYPE: []
    };
  },
  created() {
    queryService(MODEL_SYSTEM_DICTIONARY, {
      typeCode: "POSTTYPE"
    }).then((res) => {
      if (res.code === "400") {
            this.$notification.error({
              message: "参数错误",
              description: "请求出现错误，请联系管理员",
            });
          } else {
            this.POSTTYPE = res.result;
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
