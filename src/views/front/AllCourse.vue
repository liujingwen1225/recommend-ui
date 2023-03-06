<template>
  <div>
    <div style="margin: 20px 0">
      <el-form ref="form" :model="search" label-width="80px">
        <el-row :gutter="10">
          <el-col :span="6">
            <el-form-item label="课程名称">
              <el-input
                clearable
                v-model="search.name"
                autocomplete="off"
              ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="课程学校">
              <el-input
                clearable
                v-model="search.school"
                autocomplete="off"
              ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="授课老师">
              <el-input
                clearable
                v-model="search.instructor"
                autocomplete="off"
              ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item>
              <el-button type="primary" @click="load">搜索</el-button>
              <el-button type="defalut" @click="reset">重置</el-button>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="10">
          <el-col :span="6">
            <el-form-item label="课程类型">
              <el-select clearable v-model="search.type" placeholder="请选择">
                <el-option
                  v-for="item in options"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                >
                </el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
    </div>

    <CourseInfo ref="courseInfo"></CourseInfo>

    <el-table
      :data="tableData"
      border
      stripe
      :header-cell-class-name="'headerBg'"
    >
      <el-table-column
        label="课程名称"
        align="center"
        :show-overflow-tooltip="true"
        prop="link"
      >
        <template slot-scope="scope">
          <el-button type="text" @click="courseInfo(scope.row.id)">{{
            scope.row.name
          }}</el-button>
        </template>
      </el-table-column>
      <el-table-column
        label="课程学校"
        align="center"
        prop="school"
        :show-overflow-tooltip="true"
      />
      <el-table-column
        label="授课老师"
        align="center"
        prop="instructor"
        :show-overflow-tooltip="true"
        width="80"
      />
      <el-table-column
        label="课程类型"
        align="center"
        prop="type"
        :show-overflow-tooltip="true"
      />
      <el-table-column
        label="课程标签"
        align="center"
        prop="labels"
        :show-overflow-tooltip="true"
        width="70"
      />
      <el-table-column
        label="课程状态"
        align="center"
        prop="status"
        :show-overflow-tooltip="true"
      />
      <el-table-column
        label="课程评分"
        align="center"
        prop="grading"
        :show-overflow-tooltip="true"
        width="70"
      />
      <el-table-column
        label="参加人数"
        align="center"
        prop="participantsNumber"
        :show-overflow-tooltip="true"
        width="70"
      />

      <el-table-column label="操作" width="80" align="center">
        <template slot-scope="scope">
          <el-button
            v-if="scope.row.courseStatus == 1"
            type="primary"
            @click="selectCourse(scope.row)"
            >选课</el-button
          >
          <el-button
            v-if="scope.row.courseStatus == 2"
            type="warning"
            @click="cancelCourse(scope.row)"
            >退选</el-button
          >
        </template>
      </el-table-column>
    </el-table>

    <div style="padding: 10px 0">
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="pageNum"
        :page-sizes="[2, 5, 10, 20]"
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
  name: "AllCourse",
  components: { CourseInfo },
  data() {
    return {
      tableData: [],
      pageNum: 1,
      pageSize: 10,
      total: 0,
      user: localStorage.getItem("user")
        ? JSON.parse(localStorage.getItem("user"))
        : {},
      search: {
        name: null,
        type: null,
        school: null,
        instructor: null,
      },
      options: [
        {
          value: "1",
          label: "大数据与人工智能",
        },
      ],
    };
  },
  created() {
    this.courseTypeList();
    this.load();
  },
  methods: {
    // 选择课程
    selectCourse({ id, name }) {
      this.$confirm(`确认要选择 《${name}》 课程？`)
        .then((_) => {
          this.request
            .post("/course/studentCourse/" + id + "/" + this.user.id)
            .then((res) => {
              if (res.code === "200") {
                this.load();
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
      this.$confirm(`确认要取消 《${name}》 课程？`)
        .then((_) => {
          this.request
            .post("/course/cancelCourseSelection/" + id + "/" + this.user.id)
            .then((res) => {
              if (res.code === "200") {
                this.load();
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
          }
        });
    },
    // 课程信息
    courseInfo(id) {
      this.request.get("/course/" + id).then((res) => {
        this.$refs.courseInfo.form = res.data;
        this.$refs.courseInfo.dialogFormVisible = true;
      });
    },
    load() {
      this.request
        .get("/course/page", {
          params: {
            pageNum: this.pageNum,
            pageSize: this.pageSize,
            ...this.search,
          },
        })
        .then((res) => {
          this.tableData = res.data.records;
          this.total = res.data.total;
        });
    },

    reset() {
      this.search = {
        name: null,
        type: null,
        school: null,
        instructor: null,
      };
      this.load();
    },
    handleSizeChange(pageSize) {
      console.log(pageSize);
      this.pageSize = pageSize;
      this.load();
    },
    handleCurrentChange(pageNum) {
      console.log(pageNum);
      this.pageNum = pageNum;
      this.load();
    },
    download(url) {
      window.open(url);
    },
  },
};
</script>

<style scoped></style>
