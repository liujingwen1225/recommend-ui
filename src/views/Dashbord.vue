<template>
  <div>
    <el-row :gutter="10" style="margin: 20px 0 60px">
      <el-col :span="6">
        <el-card style="color: #409eff">
          <div><i class="el-icon-user" /> 今年选课用户</div>
          <div style="padding: 10px 0; text-align: center; font-weight: bold">
            {{ userNumber }}
          </div>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card style="color: #f56c6c">
          <div><i class="el-icon-reading" /> 课程数量</div>
          <div style="padding: 10px 0; text-align: center; font-weight: bold">
            {{ courseNumber }}
          </div>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card style="color: #67c23a">
          <div><i class="el-icon-data-line" /> 授课教师数量</div>
          <div style="padding: 10px 0; text-align: center; font-weight: bold">
            {{ teacherNumber }}
          </div>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card style="color: #e6a23c">
          <div><i class="el-icon-office-building" /> 授课学校数量</div>
          <div style="padding: 10px 0; text-align: center; font-weight: bold">
            {{ schoolNumber }}
          </div>
        </el-card>
      </el-col>
    </el-row>
    <el-row>
      <el-col :span="24">
        <div id="hotCoutse" style="width: 100%; height: 450px"></div>
      </el-col>
    </el-row>
    <el-row>
      <el-col :span="12">
        <div id="hotSchool" style="width: 100%; height: 450px"></div>
      </el-col>

      <el-col :span="12">
        <div id="hotTeather" style="width: 100%; height: 450px"></div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import * as echarts from "echarts";

export default {
  name: "Home",
  data() {
    return {
      // "今年选课用户")
      userNumber: 0,
      // "课程数量")
      courseNumber: 0,
      //"授课教师数量")
      teacherNumber: 0,
      //"授课学校数量")
      schoolNumber: 0,
    };
  },
  mounted() {
    // 页面元素渲染之后再触发
    this.fnChartData();
  },
  methods: {
    fnChartData() {
      this.request.get("/echarts/chartData").then((res) => {
        // "今年选课用户")
        this.userNumber = res.data.userNumber;
        // "课程数量")
        this.courseNumber = res.data.courseNumber;
        //"授课教师数量")
        this.teacherNumber = res.data.teacherNumber;
        //"授课学校数量")
        this.schoolNumber = res.data.schoolNumber;
        //热门课程
        this.fnHotCoutse(
          res.data.popularCourseNameList,
          res.data.popularCourseNumberList
        );
        //热门学校
        this.fnHotCoutse(
          res.data.popularSchoolNameList,
          res.data.popularSchoolNumberList
        );
        //热门老师
        this.fnHotCoutse(
          res.data.popularTeacherNameList,
          res.data.popularTeacherNumberList
        );
      });
    },
    /**
     * 热门课程
     * @param {*} popularCourseNameList 名称数组
     * @param {*} popularCourseNumberList 人数数组
     */
    fnHotCoutse(popularCourseNameList, popularCourseNumberList) {
      const hotCoutse = document.getElementById("hotCoutse");
      const hotCoutseChart = echarts.init(hotCoutse);
      let hotCoutseOption = {
        title: {
          text: "热门课程",
          subtext: "学生最多选择的课程",
        },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "shadow",
          },
        },
        legend: {},
        grid: {
          left: "3%",
          right: "4%",
          bottom: "3%",
          containLabel: true,
        },
        xAxis: {
          type: "value",
          boundaryGap: [0, 0.01],
        },
        yAxis: {
          type: "category",
          data: popularCourseNameList,
        },
        color: "#8299f2",
        series: [
          {
            type: "bar",
            data: popularCourseNumberList,
          },
        ],
      };
      hotCoutseChart.setOption(hotCoutseOption);
    },
    /**
     * 热门学校
     * @param {*} popularSchoolNameList 名称数组
     * @param {*} popularSchoolNumberList 人数数组
     */
    fnHotSchool(popularSchoolNameList, popularSchoolNumberList) {
      const hotSchool = document.getElementById("hotSchool");
      const hotSchoolChart = echarts.init(hotSchool);
      let hotSchoolOption = {
        title: {
          text: "热门学校",
          subtext: "学生所选课程来自的学校",
        },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "shadow",
          },
        },
        color: "#8ff9f2",
        xAxis: {
          type: "category",
          data: popularSchoolNameList,
        },
        yAxis: {
          type: "value",
        },
        series: [
          {
            data: popularSchoolNumberList,
            type: "bar",
          },
        ],
      };
      hotSchoolChart.setOption(hotSchoolOption);
    },
    /**
     * 热门老师
     * @param {*} popularTeacherNameList 名称数组
     * @param {*} popularTeacherNumberList 人数数组
     */
    fnHotTeather(popularTeacherNameList, popularTeacherNumberList) {
      const hotTeather = document.getElementById("hotTeather");
      const hotTeatherChart = echarts.init(hotTeather);
      let hotTeatherOption = {
        title: {
          text: "热门老师",
          subtext: "学生所选课程对应授课老师",
        },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "shadow",
          },
        },
        color: "#8923f2",
        xAxis: {
          type: "category",
          data: popularTeacherNameList,
        },
        yAxis: {
          type: "value",
        },
        series: [
          {
            data: popularTeacherNumberList,
            type: "bar",
          },
        ],
      };
      hotTeatherChart.setOption(hotTeatherOption);
    },
  },
};
</script>

<style scoped></style>
