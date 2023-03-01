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
            <img
              :src="item.coverImageUrl"
              alt=""
              style="width: 100%; min-height: 200px"
            />
            <div
              style="
                color: #666;
                padding: 10px;
                text-overflow: ellipsis;
                overflow: hidden;
                white-space: nowrap;
              "
            >
              {{ item.name }}
            </div>
            <div style="padding: 10px">
              <el-button type="primary" @click="cancelCourse(item)"
                >退选</el-button
              >
            </div>
          </div>
        </el-col>
      </el-row>

      <div style="padding: 10px 0">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="pageNum"
          :page-sizes="[12, 24, 48, 64]"
          :page-size="pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
        >
        </el-pagination>
      </div>
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
      pageNum: 1,
      pageSize: 12,
      total: 0,
      files: [],
    };
  },
  created() {
    this.load();
  },
  methods: {
    load() {
      this.request
        .get("/course/myCourseList", {
          params: {
            pageNum: this.pageNum,
            pageSize: this.pageSize,
          },
        })
        .then((res) => {
          this.files = res.data.records;
          this.total = res.data.total;
        });
    },
    handleSizeChange(pageSize) {
      this.pageSize = pageSize;
      this.load();
    },
    handleCurrentChange(pageNum) {
      this.pageNum = pageNum;
      this.load();
    },
    // 取消课程
    cancelCourse({ id, name }) {
      this.$confirm(`确认要取消 《${name}》 课程？`)
        .then((_) => {
          this.request
            .post("/course/cancelCourseSelection/" + id + "/" + this.user.id)
            .then((res) => {
              if (res.code === "200") {
                this.$message.success("已取消");
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
