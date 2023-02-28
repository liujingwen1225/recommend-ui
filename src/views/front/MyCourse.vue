<template>
  <div>
    <div style="margin: 20px 0">
      <el-row :gutter="10">
        <el-col
          :span="6"
          v-for="item in files"
          :key="item.id"
          style="margin-bottom: 10px"
        >
          <div style="border: 1px solid #ccc; padding-bottom: 10px">
            <img :src="item.coverImageUrl" alt="" style="width: 100%;min-height: 200px;" />
            <div style="color: #666; padding: 10px">{{ item.name }}</div>
            <div style="padding: 10px">
              <el-button type="primary" @click="fnCannel(item.id, item.name)"
                >取消</el-button
              >
            </div>
          </div>
        </el-col>
      </el-row>
    </div>
  </div>
</template>

<script>
export default {
  name: "MyCourse",
  data() {
    return {
      user: localStorage.getItem("user")
        ? JSON.parse(localStorage.getItem("user"))
        : {},
      files: [],
    };
  },
  created() {
    this.request
      .get("/course/page", {
        params: {
          pageNum: 1,
          pageSize: 12,
          name: "",
        },
      })
      .then((res) => {
        console.log(res.data.records);
        this.files = res.data.records;
      });
  },
  methods: {
    fnCannel(courseId, name) {
      this.$confirm(`确认要取消 ${name} 课程？`)
        .then((_) => {
          this.request
            .post("/course/studentCourse/" + courseId + "/" + this.user.id)
            .then((res) => {
              if (res.code === "200") {
                this.$message.success("取消成功");
              } else {
                this.$message.success(res.msg);
              }
            });
        })
        .catch((_) => {});
    },
  },
};
</script>

<style></style>
