<template>
  <div>
    <CourseInfo ref="courseInfo"></CourseInfo>

    <div style="margin: 20px 0" v-show="total != 0">
      <el-row :gutter="10">
        <el-col
          :span="6"
          v-for="item in records"
          :key="item.id"
          style="margin-bottom: 10px"
        >
          <div class="card" @click.stop="courseInfo(item.id)">
            <img
              :src="item.coverImageUrl"
              alt=""
              style="width: 100%; min-height: 200px"
            />
            <div class="card-name">
              {{ item.name }}
            </div>
            <div class="card-info">
              <div>课程评分：{{ item.grading }}</div>
              <div>参与人数：{{ item.participantsNumber }}</div>
            </div>
            <div style="padding: 10px">
              <el-button type="warning" @click.stop="cancelCourse(item)"
                >退选</el-button
              >
            </div>
          </div>
        </el-col>
      </el-row>
    </div>

    <div style="padding: 10px 0" v-show="total != 0">
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
</template>

<script>
import CourseInfo from "../../components/CourseInfo.vue";
export default {
  name: "MyCourse",
  components: { CourseInfo },
  data() {
    return {
      user: localStorage.getItem("user")
        ? JSON.parse(localStorage.getItem("user"))
        : {},
      // 列表数据
      records: [],
      pageNum: 1,
      pageSize: 12,
      total: 0,
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
          this.records = res.data.records;
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
    // 课程信息
    courseInfo(id) {
      this.request.get("/course/" + id).then((res) => {
        this.$refs.courseInfo.form = res.data;
        this.$refs.courseInfo.dialogFormVisible = true;
      });
    },
    // 取消课程
    cancelCourse({ id, name }) {
      this.$confirm(`确认要取消 《${name}》 课程？`)
        .then((_) => {
          this.request
            .post("/course/cancelCourseSelection/" + id + "/" + this.user.id)
            .then((res) => {
              if (res.code === "200") {
                this.$message.success({
                  duration: 2000,
                  showClose: true,
                  message: "已取消",
                });
                this.load();
              } else {
                this.$message.success({
                  duration: 2000,
                  showClose: true,
                  message: res.msg,
                });
              }
            });
        })
        .catch((_) => {});
    },
  },
};
</script>

<style></style>
