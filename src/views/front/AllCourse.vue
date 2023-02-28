<template>
  <div>
    <div style="margin: 10px 0">
      <el-form ref="form" :model="search" label-width="80px">
        <el-row :gutter="10">
          <el-col :span="6">
            <el-form-item label="课程名称">
              <el-input v-model="search.name" autocomplete="off"></el-input>
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
              <el-button type="warning" @click="reset">重置</el-button>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="10">
          <el-col :span="6">
            <el-form-item label="课程类型">
              <el-input
                clearable
                v-model="search.type"
                autocomplete="off"
              ></el-input>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
    </div>

    <el-table
      :data="tableData"
      border
      stripe
      :header-cell-class-name="'headerBg'"
    >
      <el-table-column label="封面图" align="center" prop="coverImageUrl">
        <template slot-scope="scope">
          <el-image
            style="width: 100px; height: 100px"
            :src="scope.row.coverImageUrl"
            fit="cover"
          ></el-image>
        </template>
      </el-table-column>

      <el-table-column
        label="课程名称"
        align="center"
        :show-overflow-tooltip="true"
        prop="link"
      >
        <template slot-scope="scope">
          <el-link :href="scope.row.link" target="_blank" type="primary">{{
            scope.row.name
          }}</el-link>
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
        label="参加人数"
        align="center"
        prop="participantsNumber"
        :show-overflow-tooltip="true"
        width="70"
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
        label="开课时间"
        align="center"
        prop="startTime"
        :show-overflow-tooltip="true"
      />
      <el-table-column
        label="结课时间"
        align="center"
        prop="endTime"
        :show-overflow-tooltip="true"
      />

      <el-table-column label="操作" width="80" align="center">
        <template slot-scope="scope">
          <el-button type="primary" @click="selectCourse(scope.row.id)"
            >选课</el-button
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
export default {
  name: "AllCourse",
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
    };
  },
  created() {
    this.load();
  },
  methods: {
    selectCourse(courseId) {
      this.request
        .post("/course/studentCourse/" + courseId + "/" + this.user.id)
        .then((res) => {
          if (res.code === "200") {
            this.$message.success("选课成功");
          } else {
            this.$message.success(res.msg);
          }
        });
    },
    load() {
      this.request
        .get("/course/page", {
          params: {
            pageNum: this.pageNum,
            pageSize: this.pageSize,
            name: "",
            // ...this.search,
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
