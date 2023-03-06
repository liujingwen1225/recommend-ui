<template>
  <el-dialog
    title="修改个人信息"
    :close-on-press-escape="false"
    :close-on-click-modal="false"
    :visible.sync="dialogFormVisible"
    width="30%"
  >
    <el-form label-width="80px" size="small" :model="form">
      <el-upload
        class="avatar-uploader"
        :action="serverIp + '/file/upload'"
        :headers="uploadHeader"
        :show-file-list="false"
        :on-success="handleAvatarSuccess"
      >
        <img v-if="form.avatarUrl" :src="form.avatarUrl" class="avatar" />
        <i v-else class="el-icon-plus avatar-uploader-icon"></i>
      </el-upload>

      <el-form-item label="用户名">
        <el-input
          v-model="form.username"
          disabled
          autocomplete="off"
        ></el-input>
      </el-form-item>
      <el-form-item label="昵称">
        <el-input
          clearable
          v-model="form.nickname"
          autocomplete="off"
        ></el-input>
      </el-form-item>
      <el-form-item
        label="邮箱"
        :rules="[
          { required: false, message: '请输入邮箱地址', trigger: 'blur' },
          {
            type: 'email',
            message: '请输入正确的邮箱地址',
            trigger: ['blur', 'change'],
          },
        ]"
      >
        <el-input clearable v-model="form.email" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item label="手机号码" maxlength="11">
        <el-input clearable v-model="form.phone" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item label="地址">
        <el-input
          clearable
          type="textarea"
          v-model="form.address"
          autocomplete="off"
        ></el-input>
      </el-form-item>
    </el-form>
    <div slot="footer" class="dialog-footer">
      <el-button @click="dialogFormVisible = false">取 消</el-button>
      <el-button type="primary" @click="save">确 定</el-button>
    </div>
  </el-dialog>
</template>

<script>
import { API_HOST } from "../config";

export default {
  name: "PersonInfo",
  props: {
    visible: Boolean,
  },
  data() {
    return {
      user: localStorage.getItem("user")
        ? JSON.parse(localStorage.getItem("user"))
        : {},
      serverIp: API_HOST,
      uploadHeader: {},
      dialogFormVisible: false,
      form: {},
    };
  },
  methods: {
    getUser() {
      this.request.get("/user/" + this.user.id).then((res) => {
        if (res.code === "200") {
          this.form = res.data;
          const userData = res.data;
          userData.token = JSON.parse(localStorage.getItem("user")).token;
          localStorage.setItem("user", JSON.stringify(userData));
          this.uploadHeader.token = userData.token;
        }
      });
    },
    save() {
      this.request.post("/user", this.form).then((res) => {
        if (res.code === "200") {
          // 触发父级更新User的方法
          this.$emit("refreshUser");

          // 更新浏览器存储的用户信息
          this.getUser();

          this.dialogFormVisible = false;
          this.$message.success({
            duration: 2000,
            showClose: true,
            message: "保存成功",
          });
        } else {
          this.$message.error({
            duration: 2000,
            showClose: true,
            message: "保存失败",
          });
        }
      });
    },
    handleAvatarSuccess(res) {
      this.form.avatarUrl = res;
    },
  },
};
</script>

<style>
.avatar-uploader {
  text-align: center;
  padding-bottom: 10px;
}
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar-uploader .el-upload:hover {
  border-color: #409eff;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 138px;
  height: 138px;
  line-height: 138px;
  text-align: center;
}
.avatar {
  width: 138px;
  height: 138px;
  display: block;
}
</style>
