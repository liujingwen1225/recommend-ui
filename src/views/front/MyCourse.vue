<template>
  <div>
    <el-dialog title="评分" :visible.sync="rateVisible" width="30%">
      <div style="text-align: center">
        <h3 v-text="'课程：《' + rate.courseName + '》'"></h3>
        <div style="margin-top: 15px">
          <el-rate
            :max="5"
            :allow-half="true"
            v-model="rate.rating"
            :colors="colors"
          >
          </el-rate>
        </div>
      </div>

      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="fnRateSave">确 定</el-button>
      </span>
    </el-dialog>

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
            <div
              style="
                padding: 10px;
                display: flex;
                justify-content: space-between;
              "
            >
              <div v-if="item.rating != null">评分：{{ item.rating }}</div>
              <el-button
                v-if="item.rating == null"
                type="defalut"
                @click.stop="fnRateOpen(item)"
                >评分</el-button
              >

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
      rateVisible: false,
      rate: {
        studentId: null,
        courseId: null,
        courseName: null,
        rating: null,
      },
      colors: ["#99A9BF", "#F7BA2A", "#FF9900"],
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
    // 打开评分
    fnRateOpen({ id, name }) {
      this.rate = {
        studentId: this.user.id,
        courseId: id,
        courseName: name,
        rating: null,
      };
      this.rateVisible = true;
    },
    // 保存评分
    fnRateSave() {
      const { courseName, rating } = this.rate;
      this.$confirm(`确认给 《${courseName}》 课程评分为：${rating} 分？`)
        .then((_) => {
          this.request.post("/course/saveRate", this.rate).then((res) => {
            if (res.code === "200") {
              this.$message.success({
                duration: 2000,
                showClose: true,
                message: "评分已记录",
              });
              this.load();
              this.rate = {
                studentId: null,
                courseId: null,
                courseName: null,
                rating: null,
              };
              this.rateVisible = false;
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
