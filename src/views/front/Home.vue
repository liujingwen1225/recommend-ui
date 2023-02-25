<template>
  <div>
    <div style="margin: 10px 0">
      <el-row :gutter="10">
        <el-col :span="6" v-for="item in files" :key="item.id" style="margin-bottom: 10px">
          <div style="border: 1px solid #ccc; padding-bottom: 10px">
            <img :src="item.coverImageUrl" alt="" style="width: 100%">
            <div style="color: #666; padding: 10px">{{ item.name }}</div>
            <div style="padding: 10px"><el-button type="primary">关注</el-button></div>
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
      files: []
    }
  },
  created() {
    this.request.get("/course/page",{
        params: {
          pageNum: 1,
          pageSize: 12,
          name: '',
        }
      }).then(res => {
      console.log(res.data.records)
      this.files = res.data.records
    })
  },
  methods: {

  }
}
</script>

<style>

</style>
