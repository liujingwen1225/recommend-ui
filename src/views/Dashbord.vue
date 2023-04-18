<template>
  <div>
    <el-row
      :gutter="10"
      style="margin: 20px 0 10px"
      v-if="user.role === 'ROLE_ADMIN'">
      <el-col :span="6">
        <el-card style="color: #409eff">
          <div style="padding: 10px 0; text-align: center; font-weight: bold"><i class="el-icon-user" /> 今年选课用户</div>
          <div style="padding: 10px 0; text-align: center; font-weight: bold">
            {{ userNumber }}
          </div>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card style="color: #f56c6c">
          <div style="padding: 10px 0; text-align: center; font-weight: bold"><i class="el-icon-reading" /> 课程数量</div>
          <div style="padding: 10px 0; text-align: center; font-weight: bold">
            {{ courseNumber }}
          </div>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card style="color: #67c23a">
          <div style="padding: 10px 0; text-align: center; font-weight: bold"><i class="el-icon-data-line" /> 授课教师数量</div>
          <div style="padding: 10px 0; text-align: center; font-weight: bold">
            {{ teacherNumber }}
          </div>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card style="color: #e6a23c">
          <div style="padding: 10px 0; text-align: center; font-weight: bold"><i class="el-icon-office-building" /> 授课学校数量</div>
          <div style="padding: 10px 0; text-align: center; font-weight: bold">
            {{ schoolNumber }}
          </div>
        </el-card>
      </el-col>
    </el-row>
    <el-row
      :gutter="10"
      style="margin: 20px 0 20px"
      v-if="user.role === 'ROLE_ADMIN'">
      <el-col :span="5">
        <el-card style="color: #409eff">
          <div style="padding: 10px 0; text-align: center; font-weight: bold"> 已开课课程数</div>
          <div style="padding: 10px 0; text-align: center; font-weight: bold">
            {{ startCourse }}
          </div>
        </el-card>
      </el-col>
      <el-col :span="5">
        <el-card style="color: #f56c6c">
          <div style="padding: 10px 0; text-align: center; font-weight: bold">即将开始的课程数</div>
          <div style="padding: 10px 0; text-align: center; font-weight: bold">
            {{ notStartCourse }}
          </div>
        </el-card>
      </el-col>
      <el-col :span="5">
        <el-card style="color: #67c23a">
          <div style="padding: 10px 0; text-align: center; font-weight: bold">已结束课程数</div>
          <div style="padding: 10px 0; text-align: center; font-weight: bold">
            {{ endCourse }}
          </div>
        </el-card>
      </el-col>
      <el-col :span="5">
        <el-card style="color: #e6a23c">
          <div style="padding: 10px 0; text-align: center; font-weight: bold"> 国家精品课程数</div>
          <div style="padding: 10px 0; text-align: center; font-weight: bold">
            {{ boutiqueCourses }}
          </div>
        </el-card>
      </el-col>
      <el-col :span="5">
        <el-card style="color: #e6723c">
          <div style="padding: 10px 0; text-align: center; font-weight: bold">大学先修程课数</div>
          <div style="padding: 10px 0; text-align: center; font-weight: bold">
            {{ purerCourses }}
          </div>
        </el-card>
      </el-col>
    </el-row>
    
    <el-row style="margin: 20px 0 30px">
      <el-col :span="24">
        <div id="hotName" style="width: 100%; height: 450px"></div>
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

    <el-row style="margin: 20px 0 30px">
      <el-col :span="24">
        <div id="hotCoutse" style="width: 100%; height: 450px"></div>
      </el-col>
    </el-row>

    <el-row>
      <el-col :span="12">
        <div id="hotSchool" style="width: 105%; height: 450px"></div>
      </el-col>

      <el-col :span="12">
        <div id="hotSchoolCourse" style="width: 100%; height: 450px"></div>
      </el-col>
    </el-row>

    <el-row>
      <el-col :span="12">
        <div id="hotTeatherSchool" style="width: 100%; height: 450px"></div>
      </el-col>
      <el-col :span="12">
        <div id="hotTeather" style="width: 100%; height: 450px"></div>
      </el-col>
    </el-row>

    <el-form ref="form" label-width="80px" style="margin: 20px 0 0">
      <el-row :gutter="10">
        <el-col :span="15">
          <el-form-item label="类型">
            <el-select
              @change="fnChartData2"
              v-model="value1"
              clearable
              placeholder="老师 or 学校 or 课程"
            >
              <el-option
                v-for="item in optionsday"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
          </el-form-item> 
          <el-form-item label="年">
            <el-select
              @change="fnChartData2"
              v-model="value2"
              clearable
              placeholder=""
            >
              <el-option
                v-for="item in optionsyear"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
          </el-form-item> 
          <el-form-item label="月">
            <el-select
              @change="fnChartData2"
              v-model="value3"
              clearable
              placeholder="4"
            >
              <el-option
                v-for="item in optionsmon"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
          </el-form-item> 
          </el-col
      >
    </el-row>
    </el-form>

    <el-row style="margin: 20px 0 30px">
      <el-col :span="24">
        <div id="monthHot" style="width: 100%; height: 450px"></div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import * as echarts from "echarts";
import 'echarts-wordcloud'
export default {
  name: "Home",
  data() {
    return {
      yearsModel:null,
        years:[],
        monthsModel:null,
        months:[],
        daysModel:null,

      value: [],
      value1: "1",
      value2: "2023",
      value3: "1",
      options: [
        {
          value: "大数据与人工智能",
          label: "大数据与人工智能",
        },
      ],
      optionsday: [
        {
          value: "1",
          label: "老师",
        },
        {
          value: "2",
          label: "学校",
        },
        {
          value: "3",
          label: "课程",
        },
      ],
      optionsyear: [
        {
          value: "2023",
          label: "2023",
        },
        {
          value: "2022",
          label: "2022",
        },
        {
          value: "2021",
          label: "2021",
        },
        {
          value: "2020",
          label: "2020",
        },
        {
          value: "2019",
          label: "2019",
        },
      ],
      optionsmon: [
      {
          value: "1",
          label: "1",
        },
        {
          value: "2",
          label: "2",
        },
        {
          value: "3",
          label: "3",
        },
        {
          value: "4",
          label: "4",
        },
        {
          value: "5",
          label: "5",
        },
        {
          value: "6",
          label: "6",
        },
        {
          value: "7",
          label: "7",
        },
        {
          value: "8",
          label: "8",
        },
        {
          value: "9",
          label: "9",
        },
        {
          value: "10",
          label: "10",
        },
        {
          value: "11",
          label: "11",
        },
        {
          value: "12",
          label: "12",
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

      startCourse: 0,
      notStartCourse: 0,
      endCourse: 0,
      boutiqueCourses: 0,
      purerCourses: 0,
      wordCould: null,

      hotCoutseChart: null,
      hotSchoolChart: null,
      hotTeatherChart:null,
      hotSchoolCourseChart: null,
      hotTeatherSchoolChart: null,
      monthHotChart: null,
      chartModels: null,
      wordChart: null,
    };
  },
  mounted() {
    const hotCoutse = document.getElementById("hotCoutse");
    this.hotCoutseChart = echarts.init(hotCoutse);
    const hotSchool = document.getElementById("hotSchool");
    this.hotSchoolChart = echarts.init(hotSchool);
    const hotTeather = document.getElementById("hotTeather");
    this.hotTeatherChart = echarts.init(hotTeather);

    const hotSchoolCourse = document.getElementById("hotSchoolCourse");
    this.hotSchoolCourseChart = echarts.init(hotSchoolCourse);

    const hotTeatherSchool = document.getElementById("hotTeatherSchool");
    this.hotTeatherSchoolChart = echarts.init(hotTeatherSchool);

    const monthHot = document.getElementById("monthHot");
    this.monthHotChart = echarts.init(monthHot);

    const hotName = document.getElementById("hotName");
    this.wordChart = echarts.init(hotName);

    this.courseTypeList();
    this.fnChartData2();
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

          this.startCourse = res.data.startCourse;
          this.notStartCourse = res.data.notStartCourse;
          this.endCourse = res.data.endCourse;
          this.boutiqueCourses = res.data.boutiqueCourses;
          this.purerCourses = res.data.purerCourses;

          this.wordCould = res.data.wordCould;
          this.chartModels = res.data.hotSchoolZb;
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
          //热门学校发布课程数占比
          this.fnHotSchoolCourse(res.data.hotSchoolZb
          );
                    //热门类型所在学校占比
          this.fnHotTeatherSchool(res.data.hotTypeZb
          );
           //词云
           this.fnwordHot(res.data.wordCould
          );
          //热门老师
          this.fnHotTeather(
            res.data.popularTeacherNameList,
            res.data.popularTeacherNumberList
          );
        });
    },
    
    fnChartData2() {
      console.log(this.value1)
      this.request
        .get("/echarts/chartYearData?type=" + this.value1+"&year=" + this.value2+"&month=" + this.value3)
        // .get("/echarts/chartYearData?year=")
        .then((res) => {
                    //月度热门
                    this.fnmonthHot(
                      res.data.popularSchoolNameList,
                      res.data.popularSchoolNumberList
          );
        });
    },
    /**
     * 热门课程
     * @param {*} popularCourseNameList 名称数组
     * @param {*} popularCourseNumberList 人数数组
     */
     fnwordHot(wordCould) {
      let hotwordOption = {
            //设置标题，居中显示
            title:{
                text: '热门课程',
                left:'center',
             },
            //数据能够点击
            tooltip:{

            },

            series:[
                {
                    // maskImage:maskResource,
                    //词的类型
                    type: 'wordCloud',
                    drawOutOfBound:true,
                    shape: 'circle',
                    //设置字符大小范围
                    sizeRange:[6,48],
                    rotationRange:[-25,50],
                    textStyle: {
                        normal:{
                          fontFamily: 'sans-serif',
                          fontWeight: 'bold',
                            //生成随机的字体颜色
                            color:function () {
                                return 'rgb(' + [
                                    Math.round(Math.random() * 160),
                                    Math.round(Math.random() * 160),
                                    Math.round(Math.random() * 160)
                                ].join(',')+')'
                            }
                        }
                    },
                    //不要忘记调用数据
                       data:wordCould

                 }
            ]

        };
      this.wordChart.setOption(hotwordOption);
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
          axisLabel:{
            formatter: function (value,index){
              if (value>1000){
                value = value /1000 +'k';
              }
              return value;
            }
          }
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
          axisLabel:{
            formatter: function (value,index){
              if (value>1000){
                value = value /1000 +'k';
              }
              return value;
            }
          }
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
     * 热门学校发布的课程数量占比
     * @param {*} popularSchoolNameList 名称数组
     * @param {*} popularSchoolNumberList 人数数组
     */
     fnHotSchoolCourse(chartModels) {
      let hotSchoolCourseOption = {
  title: {
    text: '热门学校发布的课程数量占比',
    // subtext: 'Fake Data',
    left: 'center'
  },
  tooltip: {
    trigger: 'item'
  },
  // legend: {
  //   orient: 'vertical',
  //   left: 'bottom'
  // },
  series: [
    {
      name: 'Access From',
      type: 'pie',
      radius: '50%',
      label: {
              normal: {
                show: true,
                formatter: '{b}:{d}%' //自定义显示格式(b:name, c:value, d:百分比)
              }
            },
      data: chartModels,
      emphasis: {
        itemStyle: {
          shadowBlur: 10,
          shadowOffsetX: 0,
          shadowColor: 'rgba(0, 0, 0, 0.5)'
        }
      }
    }
  ]
};
      this.hotSchoolCourseChart.setOption(hotSchoolCourseOption);
    },
    /**
     * 热门老师所在的学校占比
     * @param {*} popularSchoolNameList 名称数组
     * @param {*} popularSchoolNumberList 人数数组
     */
     fnHotTeatherSchool(data) {
      let hotTeatherSchoolOption = {
  title: {
    text: ' 热门类型课程占比',
    // subtext: 'Fake Data',
    left: 'center'
  },
  tooltip: {
    trigger: 'item'
  },
  // legend: {
  //   orient: 'vertical',
  //   left: 'left'
  // },
  series: [
    {
      name: 'Access From',
      type: 'pie',
      radius: '50%',
      label: {
              normal: {
                show: true,
                formatter: '{b}:{d}%' //自定义显示格式(b:name, c:value, d:百分比)
              }
            },
      data: data,
      emphasis: {
        itemStyle: {
          shadowBlur: 10,
          shadowOffsetX: 0,
          shadowColor: 'rgba(0, 0, 0, 0.5)'
        }
      }
    }
  ]
};
      this.hotTeatherSchoolChart.setOption(hotTeatherSchoolOption);
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
          axisLabel:{
            formatter: function (value,index){
              if (value>1000){
                value = value /1000 +'k';
              }
              return value;
            }
          }
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

    /**
     * 热门学校发布的课程数量占比
     * @param {*} popularSchoolNameList 名称数组
     * @param {*} popularSchoolNumberList 人数数组
     */
     fnmonthHot(data1,data2) {
      let monthHotOption = {
        title: {
          text: this.value2+"年"+this.value3+"月最热门",
          // subtext: "学生所选课程来自的学校",
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
          data: data1,
          axisLabel: {
            // 设置字体的倾斜角度
            interval: 0,
            rotate: 30,
          },
        },
        yAxis: {
          type: "value",
          axisLabel:{
            formatter: function (value,index){
              // if (value>1000){
              //   value = value /1000 +'k';
              // }
              return value;
            }
          }
        },
        series: [
          {
            data: data2,
            type: "bar",
          },
        ],
      };
      this.monthHotChart.setOption(monthHotOption);
    },
  },
};
</script>

<style scoped>
.el-col-5 {
  width: 20%;
}
</style>
