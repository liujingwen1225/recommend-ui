<template>
  <div style="line-height: 60px; display: flex">
    <div style="flex: 1">
      <span
        :class="collapseBtnClass"
        style="cursor: pointer; font-size: 18px"
        @click="collapse"
      ></span>

      <el-breadcrumb
        separator="/"
        style="display: inline-block; margin-left: 10px"
      >
        <el-breadcrumb-item :to="'/'">首页</el-breadcrumb-item>
        <el-breadcrumb-item>{{ currentPathName }}</el-breadcrumb-item>
      </el-breadcrumb>
    </div>
    <el-dropdown
      @command="handleCommand"
      style="width: 150px; cursor: pointer; text-align: right"
    >
      <div style="display: inline-block">
        <img
          :src="user.avatarUrl"
          alt=""
          style="
            width: 30px;
            border-radius: 50%;
            position: relative;
            top: 10px;
            right: 5px;
          "
        />
        <span>{{ user.nickname }}</span
        ><i class="el-icon-arrow-down" style="margin-left: 5px"></i>
      </div>
      <el-dropdown-menu
        slot="dropdown"
        style="width: 100px; text-align: center"
      >
        <el-dropdown-item
          style="font-size: 14px; padding: 5px 0"
          command="passwordOpen"
        >
          修改密码
        </el-dropdown-item>
        <el-dropdown-item
          style="font-size: 14px; padding: 5px 0"
          command="personOpen"
          >个人信息</el-dropdown-item
        >
        <el-dropdown-item
          style="font-size: 14px; padding: 5px 0"
          command="logout"
        >
          退出
        </el-dropdown-item>
      </el-dropdown-menu>
    </el-dropdown>
    <PersionInfo ref="persionInfo"></PersionInfo>
    <PasswordInfo ref="passwordInfo"></PasswordInfo>
  </div>
</template>

<script>
import PersionInfo from "./PersonInfo.vue";
import PasswordInfo from "./PasswordInfo.vue";
export default {
  name: "Header",
  components: { PersionInfo, PasswordInfo },
  props: {
    collapseBtnClass: String,
    user: Object,
  },
  computed: {
    currentPathName() {
      return this.$store.state.currentPathName; //需要监听的数据
    },
  },
  data() {
    return {};
  },
  methods: {
    collapse() {
      // this.$parent.$parent.$parent.$parent.collapse()  // 通过4个 $parent 找到父组件，从而调用其折叠方法
      this.$emit("asideCollapse");
    },
    logout() {
      this.$store.commit("logout");
      this.$message.success({
        duration: 2000,
        showClose: true,
        message: "退出成功",
      });
    },
    handleCommand(command) {
      if (command == "logout") {
        this.logout();
      }
      if (command == "passwordOpen") {
        this.$refs.passwordInfo.dialogFormVisible = true;
      }
      if (command == "personOpen") {
        this.$refs.persionInfo.getUser();
        this.$refs.persionInfo.dialogFormVisible = true;
      }
    },
  },
};
</script>

<style scoped></style>
