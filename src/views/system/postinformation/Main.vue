<template>
  <div>
    <s-list
      v-if="currentTab === 'list'"
      :searchCode="searchCode"
      @edit="edit"
      @search="search"
    />
    <s-form
      v-if="currentTab === 'add'"
      :formId="id"
      :type="type"
      :POSTTYPE="POSTTYPE"
      :RACES="RACES"
      :EDUCATIONS="EDUCATIONS"
      :MARITALS="MARITALS"
      :POSTSTATES="POSTSTATES"
      @edit="edit"
    />
    <s-view v-if="currentTab === 'view'" :formId="id" @edit="edit" />
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
  name: "Main_Post_Information",
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
      POSTTYPE: [],
      EDUCATIONS: [],
      RACES: [],
      MARITALS: [],
      POSTSTATES: [],
      path: {},
    };
  },
  created() {
    const that = this;
    axios
      .all([
        queryService(MODEL_SYSTEM_DICTIONARY, {
          typeCode: "RACE",
        }),
        queryService(MODEL_SYSTEM_DICTIONARY, {
          typeCode: "MARITALSTATUS",
        }),
        queryService(MODEL_SYSTEM_DICTIONARY, {
          typeCode: "EDUCATION",
        }),
        queryService(MODEL_SYSTEM_DICTIONARY, {
          typeCode: "POSTTYPE",
        }),
        queryService(MODEL_SYSTEM_DICTIONARY, {
          typeCode: "POSTSTATE",
        }),
      ])
      .then(
        axios.spread(function (acct, perms, pqs, posts, poststates) {
          if (
            acct.code === "400" ||
            perms.code === "400" ||
            pqs === "400" ||
            posts === "400" ||
            poststates === "400"
          ) {
            that.$notification.error({
              message: "参数错误",
              description: "请求出现错误，请联系管理员",
            });
          } else {
            that.RACES = acct.result;
            that.MARITALS = perms.result;
            that.EDUCATIONS = pqs.result;
            that.POSTTYPE = posts.result;
            that.POSTSTATES = poststates.result;
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
    },
  },
};
</script>
