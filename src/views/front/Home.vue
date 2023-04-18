<template>
  <div>
    <el-dialog
      title="请选择喜欢的类型"
      :visible.sync="dialogVisible"
      width="30%"
      :show-close="false"
      :close-on-press-escape="false"
      :close-on-click-modal="false"
    >
      <div>
        <el-form ref="form" label-width="120px">
          <el-row :gutter="10">
            <el-col :span="24">
              <el-form-item label="课程类型">
                <el-select
                  multiple
                  v-model="value"
                  clearable
                  placeholder="请选择"
                >
                  <el-option
                    v-for="item in options"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                  >
                  </el-option>
                </el-select>
              </el-form-item> </el-col
          ></el-row>
          <el-row :gutter="10">
            <el-col :span="24">
              <el-form-item label="学校">
                <el-select
                  multiple
                  v-model="value"
                  clearable
                  placeholder="请选择"
                >
                  <el-option
                    v-for="item in options"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                  >
                  </el-option>
                </el-select>
              </el-form-item> </el-col
          ></el-row>
          <el-row :gutter="10">
            <el-col :span="24">
              <el-form-item label="课程标签">
                <el-select
                  multiple
                  v-model="value"
                  clearable
                  placeholder="请选择"
                >
                  <el-option
                    v-for="item in options"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                  >
                  </el-option>
                </el-select>
              </el-form-item> </el-col
          ></el-row>
        </el-form>
      </div>

      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="saveCourseType">确 定</el-button>
      </span>
    </el-dialog>

    <CourseInfo ref="courseInfo"></CourseInfo>

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
              <div v-if="item.courseStatus == 2 && item.rating != null">
                评分：{{ item.rating }}
              </div>
              <el-button
                v-if="item.courseStatus == 2 && item.rating == null"
                type="defalut"
                @click.stop="fnRateOpen(item)"
                >评分</el-button
              >

              <el-button
                v-if="item.courseStatus == 1"
                type="primary"
                @click.stop="selectCourse(item)"
                >选课</el-button
              >
              <el-button
                v-if="item.courseStatus == 2"
                type="warning"
                @click.stop="cancelCourse(item)"
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
  name: "FrontHome",
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
      dialogVisible: false,
      user: localStorage.getItem("user")
        ? JSON.parse(localStorage.getItem("user"))
        : {},
      value: [],
      options: [
        {
          value: "大数据与人工智能",
          label: "大数据与人工智能",
        },
      ],
      // 列表数据
      records: [],
      pageNum: 1,
      pageSize: 12,
      total: 0,
    };
  },
  created() {
    this.request
      .get("/course/userType", {
        params: {},
      })
      .then((res) => {
        // 新用户弹出
        if (res.data && res.data.userType == "1") {
          this.courseTypeList();
        } else {
          this.indexCourse();
        }
      });
  },
  methods: {
    // 课程类型列表
    courseTypeList() {
      this.request
        .get("/course/courseTypeList", {
          params: {},
        })
        .then((res) => {
          if (res.data) {
            this.options = res.data.map((item) => ({
              value: item.type,
              label: item.type,
            }));
            this.dialogVisible = true;
          }
        });
    },
    // 确认课程类型
    saveCourseType() {
      if (this.value.length <= 0) {
        this.$message.warning({
          duration: 2000,
          showClose: true,
          message: "请选择课程类型！",
        });
        return;
      }
      if (this.value.length <= 0) {
        this.$message.warning({
          duration: 2000,
          showClose: true,
          message: "请选择学校！",
        });
        return;
      }
      if (this.value.length <= 0) {
        this.$message.warning({
          duration: 2000,
          showClose: true,
          message: "请选择课程标签！",
        });
        return;
      }
      this.$confirm("确认选择？")
        .then((_) => {
          this.request
            .post("/course/saveCourseType", {
              typeList: this.value.join(","),
            })
            .then((res) => {
              if (res.code === "200") {
                this.dialogVisible = false;
                this.indexCourse();
              } else {
                this.$message.success({
                  duration: 2000,
                  showClose: true,
                  message: res.msg,
                });
              }
            })
            .catch((e) => {
              this.$message.error({
                duration: 2000,
                showClose: true,
                message: "出错了",
              });
            });
        })
        .catch((_) => {});
    },
    // 首页课程列表
    indexCourse() {
      this.request
        .get("/course/indexCourse", {
          params: {
            pageNum: this.pageNum,
            pageSize: this.pageSize,
          },
        })
        .then((res) => {
          this.records = res.data;
          this.total = res.data.length;
        });
    },
    // 课程信息
    courseInfo(id) {
      this.request.get("/course/" + id).then((res) => {
        this.$refs.courseInfo.form = res.data;
        this.$refs.courseInfo.dialogFormVisible = true;
      });
    },
    handleSizeChange(pageSize) {
      this.pageSize = pageSize;
      this.indexCourse();
    },
    handleCurrentChange(pageNum) {
      this.pageNum = pageNum;
      this.indexCourse();
    },
    // 选择课程
    selectCourse({ id, name }) {
      this.$confirm(`确认要选择 《${name}》 课程？`)
        .then((_) => {
          this.request
            .post("/course/studentCourse/" + id + "/" + this.user.id)
            .then((res) => {
              if (res.code === "200") {
                this.indexCourse();
                this.$message.success({
                  duration: 2000,
                  showClose: true,
                  message: "选课成功",
                });
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
    // 取消课程
    cancelCourse({ id, name }) {
      this.$confirm(`确认要取消 ${name} 课程？`)
        .then((_) => {
          this.request
            .post("/course/cancelCourseSelection/" + id + "/" + this.user.id)
            .then((res) => {
              if (res.code === "200") {
                this.indexCourse();
                this.$message.success({
                  duration: 2000,
                  showClose: true,
                  message: "已取消",
                });
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
