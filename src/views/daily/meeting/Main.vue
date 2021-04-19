<template>
  <div>
    <s-list v-if="currentTab === 'list'" :searchCode="searchCode"  @edit="edit" @search="search"/>
    <s-form v-if="currentTab === 'add'"  :formId="id" :type="type" @edit="edit"/>
    <s-view v-if="currentTab === 'view'" :formId="id" :type="type" @edit="edit" />

  </div>
</template>

<script>
import SList from "./List";
import SForm from "./Form";
import SView from "./View";

export default {
  name: "Main_meeting",
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
    };
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
