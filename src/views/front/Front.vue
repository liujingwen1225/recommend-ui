<template>
  <div>
    <!--    头部-->
    <div
      style="
        display: flex;
        height: 60px;
        line-height: 60px;
        border-bottom: 1px solid #eee;
      "
    >
      <div style="width: 300px; display: flex; padding-left: 30px">
        <div style="width: 60px">
          <img
            src="../../assets/logo.png"
            alt=""
            style="width: 50px; position: relative; top: 5px; right: 0"
          />
        </div>
        <div style="flex: 1; font-weight: 600">欢迎来到课程推荐系统</div>
      </div>
      <div style="flex: 1">
        <!--        导航菜单-->
        <el-menu
          :default-active="activeIndex"
          class="el-menu-demo"
          mode="horizontal"
          router
          background-color="#ffffff"
          text-color="#909399"
          active-text-color="#409EFF"
        >
          <el-menu-item index="/front/home">首页</el-menu-item>
          <el-menu-item index="/front/dashbord">数据可视化</el-menu-item>
          <el-menu-item index="/front/all-course">全部课程</el-menu-item>
          <el-menu-item index="/front/my-course">我的课程</el-menu-item>
        </el-menu>
      </div>
      <div style="width: 200px">
        <div
          v-if="!user.username"
          style="text-align: right; padding-right: 30px"
        >
          <el-button @click="$router.push('/login')">登录</el-button>
          <el-button @click="$router.push('/register')">注册</el-button>
        </div>
        <div v-else>
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
        </div>
      </div>
    </div>

    <PersionInfo ref="persionInfo"></PersionInfo>
    <PasswordInfo ref="passwordInfo"></PasswordInfo>

    <div style="width: 1000px; margin: 0 auto">
      <router-view />
    </div>
  </div>
</template>

<script>
import PersionInfo from "./../../components/PersonInfo.vue";
import PasswordInfo from "./../../components/PasswordInfo.vue";
export default {
  name: "Front",
  components: { PersionInfo, PasswordInfo },
  data() {
    return {
      activeIndex: "/front/home",
      user: localStorage.getItem("user")
        ? JSON.parse(localStorage.getItem("user"))
        : {},
    };
  },
  created() {
    this.activeIndex = location.pathname;
  },
  methods: {
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
        this.$refs.persionInfo.getUser()
        this.$refs.persionInfo.dialogFormVisible = true;
      }
    },
  },
};
</script>

<style>
.item {
  display: inline-block;
  width: 100px;

  text-align: center;
  cursor: pointer;
}
.item a {
  color: #1e90ff;
}
.item:hover {
  background-color: LightPink;
}
</style>
