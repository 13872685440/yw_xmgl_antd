<template>
  <div>
    <page-view :title="false">
      <div class="home">
        <div class="gutter-example">
          <div class="gutter-box">
            <h2 class="h-title">
              待办任务
            </h2>
            <s-table ref="table" size="default" :columns="columns" :data="loadData" :pageSize="20">
              <span slot="name" slot-scope="text, item">
                <span :style="{color:item.colorCode}">{{text}}</span>
              </span>
              <span slot="action" slot-scope="text, item">
                <template>
                  <span>
                    <span
                      v-if="item.taskType === '待领取'"
                      style="color: #69aa46"
                      @click="receiveTask(item, 1)"
                    >
                      领取任务
                      <a-divider type="vertical" />
                    </span>
                    <span
                      v-if="item.taskType === '已领取'"
                      style="color: #ff892a"
                      @click="receiveTask(item, 2)"
                    >
                      取消领取
                      <a-divider type="vertical" />
                    </span>
                    <span v-if="item.taskType === '待办' || item.taskType === '已领取'">
                      <router-link
                        :to="{
                            name: item.view,
                            params: {
                              id: item.id,
                              keyValue: item.keyValue,
                              task_crtUid: item.crtUid,
                            },
                          }"
                      >处理任务</router-link>
                      <a-divider type="vertical" />
                    </span>
                    <span v-if="item.taskId">
                      <a
                        @click="
                            $refs.simage.add({
                              processInstanceId: item.processInstanceId,
                            })
                          "
                        title="查看流程图"
                        style="color: #a069c3"
                      >查看流程图</a>
                    </span>
                  </span>
                </template>
              </span>
            </s-table>
          </div>
        </div>
      </div>
      <s-image ref="simage" :path="path.image_path" title="查看流程图" />
    </page-view>
    <setting-drawer v-if="isDesktop()"></setting-drawer>
  </div>
</template>
<script>
import { timeFix } from "@/utils/util";
import { mixin, mixinDevice } from "@/utils/mixin";
import { PageView } from "@/layouts";
import { queryService, toService } from "@/api/manage";
import { STable } from "@/components";
import SImage from "@/components/Image/Image";
import SettingDrawer from "@/components/SettingDrawer";

export default {
  name: "Workplace",
  mixins: [mixin, mixinDevice],
  components: {
    PageView,
    STable,
    SImage,
    SettingDrawer
  },
  data() {
    return {
      timeFix: timeFix(),
      avatar: "",
      user: {},
      loading: true,
      path: {},
      task_data: [],
      task_totalCount: 0,
      xsImg:'/workplace/img-01.png',
      columns: [
        {
          title: "任务名称",
          dataIndex: "taskName",
          key: "taskName"
        },
        {
          title: "任务节点",
          dataIndex: "nodeName",
          key: "nodeName"
        },
        {
          title: "发起人",
          dataIndex: "firsttransactor",
          key: "firsttransactor"
        },
        {
          title: "上一步办理人",
          key: "previoustransactor",
          dataIndex: "previoustransactor"
        },
        {
          title: "上一步办理时间",
          key: "ct",
          dataIndex: "ct"
        },
        {
          title: "操作",
          key: "action",
          scopedSlots: { customRender: "action" }
        }
      ],
      loadData: parameter => {
        parameter.params = this.queryParam;
        return queryService(this.path.task_path,parameter).then(res => {
           return res.result;
        });
      }
    };
  },
  computed: {
    userInfo() {
      return this.$store.getters.userInfo;
    }
  },
  created() {
    const prefix = "/" + this.GLOBAL.MODEL_SYSTEM;
    this.user = this.userInfo;
    this.avatar = this.userInfo.avatar;
    this.path.task_path = prefix + "/taskext/main";
    this.path.receive_path = prefix + "/taskext/receive";
    this.path.cancelreceive_path = prefix + "/taskext/cancelreceive";
    this.path.image_path = prefix + "/processdefinitionbean/viewimage_now";
  },
  beforeRouteEnter(to, from, next) {
    next(vm => {
      vm.loadData();
    });
  },
  methods: {
    async receiveTask(item, i) {
      const data = { id: item.id };
      if (i == 1) {
        await toService("POST", this.path.receive_path, data).then(res => {
          if (res.code === "400") {
            this.$notification.error({
              message: "任务领取失败",
              description: "任务已被领取"
            });
          } else if (res.code === "200") {
            this.$notification.success({
              message: "任务领取成功"
            });
            this.renderTable();
          }
        });
      } else {
        await toService("POST", this.path.cancelreceive_path, data).then(
          res => {
            if (res.code === "200") {
              this.$notification.success({
                message: "取消领取成功"
              });
              this.renderTable();
            }
          }
        );
      }
    }
  }
};
</script>
<style lang="less" scoped>
._content {
  margin: 10px 10px !important;
}
@import url("/workplace/style.css");
</style>
