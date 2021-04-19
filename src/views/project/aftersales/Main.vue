<template>
  <div>
    <s-list v-if="currentTab === 'list'" :searchCode="searchCode" :NATURE="NATURE" :XMSTATE="XMSTATE" @edit="edit" @search="search"/>
    <s-form v-if="currentTab === 'add'"  :formId="id" :type="type" :NATURE="NATURE" :XMSTATE="XMSTATE" @edit="edit"/>
    <s-view v-if="currentTab === 'view'" :formId="id" :type="type" :XMSTATE="XMSTATE" @edit="edit" />
  </div>
</template>

<script>
import SList from "./List";
import SForm from "./Form";
import SView from "./View";

import { queryService } from "@/api/manage";
import { MODEL_SYSTEM_DICTIONARY } from "@/store/mutation-types";
import axios from "axios";

export default {
  name: "Main_Aftersales",
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
      NATURE: [],
      XMSTATE:[]
    };
  },
  created() {
    const that = this;
    axios.all([
        queryService(MODEL_SYSTEM_DICTIONARY, {
          typeCode: "NATURE",
        }),
        queryService(MODEL_SYSTEM_DICTIONARY, {
          typeCode: "XMSTATE",
        }),
      ])
      .then(axios.spread(function (nature, xmstates) {
          if (nature === "400" || xmstates === "400") {
            that.$notification.error({
              message: "参数错误",
              description: "请求出现错误，请联系管理员",
            });
          } else {
            that.NATURE = nature.result;
            that.XMSTATE = xmstates.result;
          }
          that.spinning = false;
        })
      );
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
