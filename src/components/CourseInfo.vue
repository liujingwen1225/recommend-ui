<template>
  <el-dialog title="课程信息" :visible.sync="dialogFormVisible" width="60%">
    <el-form label-width="80px" size="small">
      <el-row :gutter="10">
        <el-col :span="12">
          <el-form-item label="封面图">
            <el-image
              style="width: 100px; height: 100px"
              :src="form.coverImageUrl"
              fit="cover"
            ></el-image>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row :gutter="10">
        <el-col :span="12">
          <el-form-item label="课程名称">
            <el-link :href="form.link" target="_blank" type="primary">{{
              form.name
            }}</el-link>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="课程名称">
            <span>{{ form.school }}</span>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row :gutter="10">
        <el-col :span="12">
          <el-form-item label="课程类型">
            <span>{{ form.type }}</span>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="授课老师">
            <span>{{ form.instructor }}</span>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row :gutter="10">
        <el-col :span="12">
          <el-form-item label="参加人数">
            <span>{{ form.participantsNumber }}</span>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="课程标签">
            <span>{{ form.labels }}</span>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row :gutter="10">
        <el-col :span="12">
          <el-form-item label="课程状态">
            <span>{{ form.status }}</span>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="课程评分">
            <span>{{ form.grading }}</span>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row :gutter="10">
        <el-col :span="12">
          <el-form-item label="开课时间">
            <span>{{ form.startTime }}</span>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="结课时间">
            <span>{{ form.endTime }}</span>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row :gutter="10">
        <el-col :span="24">
          <el-form-item label="概述">
            <span>{{ form.overview }}</span>
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>
    <div slot="footer" class="dialog-footer">
      <el-button @click="dialogFormVisible = false">取 消</el-button>
    </div>
  </el-dialog>
</template>

<script>
export default {
  name: "CourseInfo",
  props: {
    visible: Boolean,
  },
  data() {
    return {
      form: {},
      dialogFormVisible: false,
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
