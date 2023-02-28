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
    <div style="margin: 10px 0">
      <el-button
        type="primary"
        @click="handleAdd"
        v-if="user.role === 'ROLE_ADMIN'"
        >新增 <i class="el-icon-circle-plus-outline"></i
      ></el-button>
      <el-popconfirm
        class="ml-5"
        confirm-button-text="确定"
        cancel-button-text="我再想想"
        icon="el-icon-info"
        icon-color="red"
        title="您确定批量删除这些数据吗？"
        @confirm="delBatch"
      >
        <el-button type="danger" slot="reference"
          >批量删除 <i class="el-icon-remove-outline"></i
        ></el-button>
      </el-popconfirm>
    </div>
    <el-table
      :data="tableData"
      border
      stripe
      :header-cell-class-name="'headerBg'"
      @selection-change="handleSelectionChange"
    >
      <el-table-column type="selection" width="55"></el-table-column>

      <el-table-column label="封面图" align="center" prop="coverImageUrl">
        <template slot-scope="scope">
          <el-image
            style="width: 100px; height: 100px"
            :src="scope.row.coverImageUrl"
            fit="cover"
          ></el-image>
        </template>
      </el-table-column>

      <el-table-column label="课程链接" align="center" prop="link" width="70">
        <template slot-scope="scope">
          <el-link :href="scope.row.link" target="_blank" type="primary"
            >链接</el-link
          >
        </template>
      </el-table-column>

      <el-table-column
        label="课程名称"
        align="center"
        prop="name"
        :show-overflow-tooltip="true"
      />
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

      <el-table-column label="操作" width="280" align="center">
        <template slot-scope="scope">
          <el-button type="primary" @click="selectCourse(scope.row.id)"
            >选课</el-button
          >
          <el-button
            type="success"
            @click="handleEdit(scope.row)"
            v-if="user.role === 'ROLE_ADMIN'"
            >编辑 <i class="el-icon-edit"></i
          ></el-button>
          <el-popconfirm
            class="ml-5"
            confirm-button-text="确定"
            cancel-button-text="我再想想"
            icon="el-icon-info"
            icon-color="red"
            title="您确定删除吗？"
            @confirm="del(scope.row.id)"
          >
            <el-button
              type="danger"
              slot="reference"
              v-if="user.role === 'ROLE_ADMIN'"
              >删除 <i class="el-icon-remove-outline"></i
            ></el-button>
          </el-popconfirm>
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

    <el-dialog title="课程信息" :visible.sync="dialogFormVisible" width="30%">
      <el-form label-width="80px" size="small">
        <el-form-item label="课程名称">
          <el-input v-model="form.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="课程学校">
          <el-input v-model="form.school" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="授课老师">
          <el-input v-model="form.instructor" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="课程类型">
          <el-input v-model="form.type" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="参加人数">
          <el-input
            v-model="form.participantsNumber"
            autocomplete="off"
          ></el-input>
        </el-form-item>
        <el-form-item label="课程标签">
          <el-input v-model="form.labels" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="课程状态">
          <el-input v-model="form.status" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="课程评分">
          <el-input v-model="form.grading" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="开课时间">
          <el-input v-model="form.startTime" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="结课时间">
          <el-input v-model="form.endTime" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="课程链接">
          <el-input v-model="form.link" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="封面图">
          <el-input v-model="form.coverImageUrl" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="save">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "Course",
  data() {
    return {
      form: {},
      tableData: [],
      multipleSelection: [],
      pageNum: 1,
      pageSize: 10,
      total: 0,
      dialogFormVisible: false,
      teachers: [],
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

      this.request.get("/user/role/ROLE_TEACHER").then((res) => {
        this.teachers = res.data;
      });
    },
    changeEnable(row) {
      this.request.post("/course/update", row).then((res) => {
        if (res.code === "200") {
          this.$message.success("操作成功");
        }
      });
    },
    handleAdd() {
      this.dialogFormVisible = true;
      this.form = {};
    },
    handleEdit(row) {
      this.form = JSON.parse(JSON.stringify(row));
      this.dialogFormVisible = true;
    },
    del(id) {
      this.request.delete("/course/" + id).then((res) => {
        if (res.code === "200") {
          this.$message.success("删除成功");
          this.load();
        } else {
          this.$message.error("删除失败");
        }
      });
    },
    handleSelectionChange(val) {
      console.log(val);
      this.multipleSelection = val;
    },
    delBatch() {
      let ids = this.multipleSelection.map((v) => v.id); // [{}, {}, {}] => [1,2,3]
      this.request.post("/course/del/batch", ids).then((res) => {
        if (res.code === "200") {
          this.$message.success("批量删除成功");
          this.load();
        } else {
          this.$message.error("批量删除失败");
        }
      });
    },
    save() {
      this.request.post("/course", this.form).then((res) => {
        if (res.code === "200") {
          this.$message.success("保存成功");
          this.dialogFormVisible = false;
          this.load();
        } else {
          this.$message.error("保存失败");
        }
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
