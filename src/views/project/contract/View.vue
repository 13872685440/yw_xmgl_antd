<template>
  <div class="page-header-index-wide">
    <a-card :bordered="false" :bodyStyle="{ padding: '16px 0', height: '100%' }" :style="{ height: '100%' }" >
      <div class="account-settings-info-main" :class="device">
        <div class="account-settings-info-left">
          <a-menu :mode="device == 'mobile' ? 'horizontal' : 'inline'"
            :style="{border: '0',width: device == 'mobile' ? '560px' : 'auto'}"
            :selectedKeys="selectedKeys" type="inner" @click="onClick">
              <a-menu-item key="1">基本信息</a-menu-item>
              <a-menu-item key="2">销售合同扫描 
                <a-badge class="login-form-forgot" :style="productState(base.htsmState_code)"/>
              </a-menu-item>
              <a-menu-item key="3">开票与收款记录
                <a-badge class="login-form-forgot" :style="productState(base.skState_code)"/>
                <a-badge class="login-form-forgot" :style="productState(base.kpState_code)"/>
              </a-menu-item>            
              <a-menu-item key="5">发货记录
                <a-badge class="login-form-forgot" :style="productState(base.fhState_code)"/>
              </a-menu-item>
              <a-menu-item key="6">售中支持记录
                <a-badge class="login-form-forgot" :style="productState(base.szzcState_code)"/>
              </a-menu-item>
              <a-menu-item key="7">销售合同结算
                <a-badge  class="login-form-forgot" :style="productState(base.htjsState_code)"/>
              </a-menu-item>
              <a-menu-item key="0">返回列表</a-menu-item>
          </a-menu>
        </div>
        <div class="account-settings-info-right">
          <s-base-view v-if="key === '1'" :base="base" :type="type" :formId="formId" :XMSTATE="XMSTATE"></s-base-view>
          <s-xshtlist v-if="key === '2'" :id="formId" :state="base.htsmState_code" :isview="true"></s-xshtlist>
          <s-skkplistview v-if="key === '3'" :id="formId" :base="base" ></s-skkplistview>
          <s-fhjllist v-if="key === '5'" :id="formId" :state="base.fhState_code" :isview="true"></s-fhjllist>
          <s-szzclist v-if="key === '6'" :id="formId" :state="base.szzcState_code" :isview="true"></s-szzclist>
          <s-htjs-view v-if="key === '7'" :cid="formId" :state="base.htjsState_code" ></s-htjs-view>
        </div>
      </div>
    </a-card>
  </div>
</template>

<script>
import SBaseView from "./BaseView";
import SSkkplistview from "./Skkplistview";
import SXshtlist from "./Xshtlist";
import SFhjllist from "./Fhjllist";
import SSzzclist from "./Szzclist";
import SHtjsView from "./HtjsView";

import { queryService, back_to_list } from "@/api/manage";
import { mixinDevice } from "@/utils/mixin.js";

export default {
  name: "Contract_View",
  props: ["formId","type","XMSTATE"],
  components: { 
    SBaseView,
    SSkkplistview,
    SXshtlist,
    SFhjllist,
    SSzzclist,
    SHtjsView
  },
  mixins: [mixinDevice],
  data() {
    return {
      mode: "inline",
      selectedKeys: [], 
      key: "",
      base: {},
      postrole: {},
      path: {},
    };
  },
  async created() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.path.edit_path = prefix + "/contract/edit";
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
