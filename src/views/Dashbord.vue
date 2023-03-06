<template>
  <div>
    <el-row
      :gutter="10"
      style="margin: 20px 0 60px"
      v-if="user.role === 'ROLE_ADMIN'"
    >
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

    <el-form ref="form" label-width="80px" style="margin: 20px 0 0">
      <el-row :gutter="10">
        <el-col :span="24">
          <el-form-item label="课程类型">
            <el-select
              @change="fnChartData"
              multiple
              v-model="value"
              clearable
              placeholder="默认不区分"
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

    <el-row style="margin: 20px 0 60px">
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
      value: [],
      options: [
        {
          value: "大数据与人工智能",
          label: "大数据与人工智能",
        },
      ],
      user: localStorage.getItem("user")
        ? JSON.parse(localStorage.getItem("user"))
        : {},
      // "今年选课用户")
      userNumber: 0,
      // "课程数量")
      courseNumber: 0,
      //"授课教师数量")
      teacherNumber: 0,
      //"授课学校数量")
      schoolNumber: 0,
      hotCoutseChart: null,
      hotSchoolChart: null,
    };
  },
  mounted() {
    const hotCoutse = document.getElementById("hotCoutse");
    this.hotCoutseChart = echarts.init(hotCoutse);
    const hotSchool = document.getElementById("hotSchool");
    this.hotSchoolChart = echarts.init(hotSchool);
    const hotTeather = document.getElementById("hotTeather");
    this.hotTeatherChart = echarts.init(hotTeather);
    this.courseTypeList();
    // 页面元素渲染之后再触发
    this.fnChartData();
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
          }
        });
    },
    fnChartData() {
      this.request
        .get("/echarts/chartData?typeList=" + this.value.join(","))
        .then((res) => {
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
          this.fnHotSchool(
            res.data.popularSchoolNameList,
            res.data.popularSchoolNumberList
          );
          //热门老师
          this.fnHotTeather(
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
      this.hotCoutseChart.setOption(hotCoutseOption);
    },
    /**
     * 热门学校
     * @param {*} popularSchoolNameList 名称数组
     * @param {*} popularSchoolNumberList 人数数组
     */
    fnHotSchool(popularSchoolNameList, popularSchoolNumberList) {
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
          axisLabel: {
            // 设置字体的倾斜角度
            interval: 0,
            rotate: 30,
          },
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
      this.hotSchoolChart.setOption(hotSchoolOption);
    },
    /**
     * 热门老师
     * @param {*} popularTeacherNameList 名称数组
     * @param {*} popularTeacherNumberList 人数数组
     */
    fnHotTeather(popularTeacherNameList, popularTeacherNumberList) {
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
          axisLabel: {
            // 设置字体的倾斜角度
            interval: 0,
            rotate: 30,
          },
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
      this.hotTeatherChart.setOption(hotTeatherOption);
    },
  },
};
</script>

<style scoped></style>
