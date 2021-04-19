<template>
  <div class="page-header-index-wide">
    <a-card :bordered="false" :bodyStyle="{ padding: '16px 0', height: '100%' }" :style="{ height: '100%' }" >
      <div class="account-settings-info-main" :class="device">
        <div class="account-settings-info-left">
          <a-menu :mode="device == 'mobile' ? 'horizontal' : 'inline'"
            :style="{border: '0',width: device == 'mobile' ? '560px' : 'auto'}"
            :selectedKeys="selectedKeys" type="inner" @click="onClick">
            <a-menu-item key="1">基本信息</a-menu-item>
            <a-menu-item key="2">售后往来记录
              <a-badge class="login-form-forgot" :style="productState(base.shlwState_code)"/>
            </a-menu-item>
            <a-menu-item key="3">收款与开票记录
              <a-badge class="login-form-forgot" :style="productState(base.skykpState_code)"/>
            </a-menu-item>
            <a-menu-item key="0">返回列表</a-menu-item>
          </a-menu>
        </div>
        <div class="account-settings-info-right">
          <s-base v-if="key === '1'" :base="base" :NATURE="NATURE" :formId="formId" @changeForm="changeForm"></s-base>
          <s-shwljl-list v-if="key === '2'" :id="formId" :state="base.shlyState_code" @changeForm="changeForm" :isview="false" />
          <s-skykp-list v-if="key === '3'" :id="formId" :state="base.skykpState_code" @changeForm="changeForm" :isview="false" />
        </div>
      </div>
    </a-card>
  </div>
</template>

<script>
import SBase from "./Base";
import SShwljlList from "./ShwljlList";
import SSkykpList from "./SkykpList";

import { queryService, back_to_list } from "@/api/manage";
import { mixinDevice } from "@/utils/mixin.js";

export default {
  name: "Aftersales_Form",
  props: [
    "formId",
    "type",
    "NATURE",
    "XMSTATE"
  ],
  components: {
    SBase,
    SShwljlList,
    SSkykpList,
  },
  mixins: [mixinDevice],
  data() {
    return {
      mode: "inline", // horizontal  inline
      selectedKeys: [],
      key: "",
      base: {},
      postrole: {},
      path: {},
    };
  },
  async created() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.edit_path = prefix + "/aftersales/" + this.type;

    this.selectedKeys = ["1"];
    await queryService(this.path.edit_path, { id: this.formId }).then((res) => {
      this.base = res.result;
    });
    this.key = "1";
  },
  methods: {
     productState(code) {
      var stype = "";
      this.XMSTATE.forEach((item) => {
        if(code==undefined){
          stype = "border:1px solid #d9d9d9;"
        }else if(code==item.code){
          stype = "background-color:"+item.colorCode;
        }
      })
       return stype;
    },
    onClick(e) {
      if (e.key === "0") {
        back_to_list(this);
      } else if (e.key != "1") {
        if (!this.base.id) {
          this.$message.warning("请先保存基本信息");
          this.selectedKeys = ["1"];
          return;
        }
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
   .login-form-forgot {
    float: right;
    width: 10px;
    height: 10px;
    display: inline-block;
    vertical-align: middle;
    border-radius: 50%;
    line-height: inherit;
    margin: 8px 8px 0 0 ;
  }

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
