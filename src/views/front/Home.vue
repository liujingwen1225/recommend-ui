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
                <el-select v-model="value" clearable placeholder="请选择">
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
          v-for="item in files"
          :key="item.id"
          style="margin-bottom: 10px"
        >
          <div style="border: 1px solid #ccc; padding-bottom: 10px">
            <img :src="item.coverImageUrl" alt="" style="width: 100%" />
            <div style="color: #666; padding: 10px">{{ item.name }}</div>
            <div style="padding: 10px">
              <el-button type="primary">选课</el-button>
            </div>
          </div>
        </el-col>
      </el-row>
    </div>
  </div>
</template>

<script>
export default {
  name: "FrontHome",
  data() {
    return {
      dialogVisible: false,
      value: null,
      options: [
        {
          value: "选项1",
          label: "黄金糕",
        },
        {
          value: "选项2",
          label: "双皮奶",
        },
        {
          value: "选项3",
          label: "蚵仔煎",
        },
        {
          value: "选项4",
          label: "龙须面",
        },
        {
          value: "选项5",
          label: "北京烤鸭",
        },
      ],
      files: [],
    };
  },
  created() {
    this.dialogVisible = true;
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
    handleClose(done) {
      this.$confirm("确认关闭？")
        .then((_) => {
          done();
        })
        .catch((_) => {});
    },
  },
};
</script>

<style></style>
