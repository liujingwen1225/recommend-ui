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
            <img :src="item.coverImageUrl" alt="" style="width: 100%" />
            <div style="color: #666; padding: 10px">{{ item.name }}</div>
            <div style="padding: 10px">
              <el-button type="primary" @click="fnCannel(item.id)">取消</el-button>
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
      dialogVisible: false,
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
    fnCannel(id) {
      this.$confirm("确认关闭？" + id)
        .then((_) => {
          console.log('ok')
        })
        .catch((_) => {});
    },
  },
};
</script>

<style></style>
