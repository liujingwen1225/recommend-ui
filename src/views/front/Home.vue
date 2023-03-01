<template>
  <div>
    <el-dialog
      title="提示"
      :visible.sync="dialogVisible"
      width="30%"
      :before-close="handleClose"
    >
      <div>
        <el-form ref="form" label-width="120px">
          <el-row :gutter="10">
            <el-col :span="24">
              <el-form-item label="请选择课程类型">
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
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogVisible = false"
          >确 定</el-button
        >
      </span>
    </el-dialog>

    <div style="margin: 20px 0">
      <el-row :gutter="10">
        <el-col
          :span="6"
          v-for="item in records"
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
              <el-button type="primary" @click="selectCourse(item)"
                >选课</el-button
              >
            </div>
          </div>
        </el-col>
      </el-row>
    </div>

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
</template>

<script>
export default {
  name: "FrontHome",
  data() {
    return {
      dialogVisible: false,
      user: localStorage.getItem("user")
        ? JSON.parse(localStorage.getItem("user"))
        : {},
      value: "",
      options: [
        {
          value: "1",
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
    // 新用户弹出
    if (this.user.userType == 1) {
      this.courseTypeList();
    }
    this.indexCourse();
  },
  methods: {
    handleClose(done) {
      this.$confirm("确认关闭？")
        .then((_) => {
          done();
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
            this.dialogVisible = true;
          }
        });
    },
    // 首页课程列表
    indexCourse() {
      this.request
        .get("/course/indexCourse", {
          params: {
            pageNum: this.pageNum,
            pageSize: this.pageSize,
            typeList: "大数据与人工智能,程序设计与开发",
          },
        })
        .then((res) => {
          this.records = res.data;
          this.total = res.data.length;
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
                this.$message.success("选课成功");
              } else {
                this.$message.success(res.msg);
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
