<template>
  <div class="page-header-index-wide">
    <a-card :bordered="false" :bodyStyle="{ padding: '16px 0', height: '100%' }" :style="{ height: '100%' }">
      <div class="account-settings-info-main" :class="device">
        <div class="account-settings-info-left">
          <a-menu
            :mode="device == 'mobile' ? 'horizontal' : 'inline'"
            :style="{border: '0',width: device == 'mobile' ? '560px' : 'auto',}"
            :selectedKeys="selectedKeys" type="inner"  @click="onClick"
          >
            <a-menu-item key="1">基本信息</a-menu-item>
            <a-menu-item key="0">返回列表</a-menu-item>
          </a-menu>
        </div>
        <div class="account-settings-info-right">
          <s-base-view v-if="key === '1'" :base="base" @changeForm="changeForm" :type="type" :formId="formId" ></s-base-view>
        </div>
      </div>
    </a-card>
  </div>
</template>

<script>
import SBaseView from "./BaseView";
import { queryService, back_to_list } from "@/api/manage";
import { mixinDevice } from "@/utils/mixin.js";

export default {
  name: "Postinformation_View",
  props: [
    "formId",
    "type",
    "POSTTYPE",
    "EDUCATIONS",
    "RACES",
    "MARITALS",
    "POSTSTATES",
  ],
  components: {
    SBaseView,
  },
  mixins: [mixinDevice],
  data() {
    return {
      // horizontal  inline
      mode: "inline",
      selectedKeys: [],
      key: "",
      base: {},
      path: {},
    };
  },
  async created() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.edit_path = prefix + "/postinformation/edit";

    this.selectedKeys = ["1"];
    await queryService(this.path.edit_path, { id: this.formId }).then((res) => {
      this.base = res.result;
    });
    this.key = "1";
  },
  methods: {
    onClick(e) {
      if (e.key === "0") {
        back_to_list(this);
      }
      this.selectedKeys = [e.key];
      this.key = e.key;
    },
    changeForm(data) {
      if (data.name === "base") {
        this.base = data.data;
      }
    },
  },
};
</script>

<style lang="less" scoped>
.account-settings-info-main {
  width: 100%;
  display: flex;
  height: 100%;
  overflow: auto;

  &.mobile {
    display: block;

    .account-settings-info-left {
      border-right: unset;
      border-bottom: 1px solid #e8e8e8;
      width: 100%;
      height: 50px;
      overflow-x: auto;
      overflow-y: scroll;
    }
    .account-settings-info-right {
      padding: 20px 40px;
    }
  }

  .account-settings-info-left {
    border-right: 1px solid #e8e8e8;
    width: 224px;
  }

  .account-settings-info-right {
    flex: 1 1;
    padding: 8px 40px;

    .account-settings-info-title {
      color: rgba(0, 0, 0, 0.85);
      font-size: 20px;
      font-weight: 500;
      line-height: 28px;
      margin-bottom: 12px;
    }
    .account-settings-info-view {
      padding-top: 12px;
    }
  }
}
</style>
