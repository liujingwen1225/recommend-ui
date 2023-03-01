<template>
  <div>
    <el-row :gutter="10" style="margin: 20px 0 60px">
      <el-col :span="6">
        <el-card style="color: #409eff">
          <div><i class="el-icon-user-solid" /> 今年选课用户</div>
          <div style="padding: 10px 0; text-align: center; font-weight: bold">
            5839
          </div>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card style="color: #f56c6c">
          <div><i class="el-icon-money" /> 课程数量</div>
          <div style="padding: 10px 0; text-align: center; font-weight: bold">
            923
          </div>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card style="color: #67c23a">
          <div><i class="el-icon-bank-card" /> 授课教师数量</div>
          <div style="padding: 10px 0; text-align: center; font-weight: bold">
            82
          </div>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card style="color: #e6a23c">
          <div><i class="el-icon-s-shop" /> 授课学校数量</div>
          <div style="padding: 10px 0; text-align: center; font-weight: bold">
            382
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
    return {};
  },
  mounted() {
    // 页面元素渲染之后再触发
    var option = {
      title: {
        text: "各季度会员数量统计",
        subtext: "趋势图",
        left: "center",
      },
      tooltip: {
        trigger: "item",
      },
      legend: {
        orient: "vertical",
        left: "left",
      },
      xAxis: {
        type: "category",
        data: ["第一季度", "第二季度", "第三季度", "第四季度"],
      },
      yAxis: {
        type: "value",
      },
      series: [
        {
          name: "星巴克",
          data: [],
          type: "bar",
        },
        {
          name: "星巴克",
          data: [],
          type: "line",
        },
        {
          name: "瑞幸咖啡",
          data: [],
          type: "bar",
        },
        {
          name: "瑞幸咖啡",
          data: [],
          type: "line",
        },
      ],
    };

    // 饼图

    var pieOption = {
      title: {
        text: "各季度会员数量统计",
        subtext: "比例图",
        left: "center",
      },
      tooltip: {
        trigger: "item",
        formatter: "{a} <br/>{b} : {c} ({d}%)",
      },
      legend: {
        orient: "vertical",
        left: "left",
      },
      series: [
        {
          name: "星巴克",
          type: "pie",
          radius: "55%",
          center: ["25%", "70%"],
          label: {
            //饼图图形上的文本标签
            normal: {
              show: true,
              position: "inner", //标签的位置
              textStyle: {
                fontWeight: 300,
                fontSize: 14, //文字的字体大小
                color: "#fff",
              },
              formatter: "{c}({d}%)",
            },
          },
          data: [], // 填空
          emphasis: {
            itemStyle: {
              shadowBlur: 10,
              shadowOffsetX: 0,
              shadowColor: "rgba(0, 0, 0, 0.5)",
            },
          },
        },
        {
          name: "瑞幸咖啡",
          type: "pie",
          radius: "50%",
          center: ["75%", "50%"],
          label: {
            //饼图图形上的文本标签
            normal: {
              show: true,
              position: "inner", //标签的位置
              textStyle: {
                fontWeight: 300,
                fontSize: 14, //文字的字体大小
                color: "#fff",
              },
              formatter: "{d}%",
            },
          },
          data: [
            { name: "第一季度", value: 5 },
            { name: "第二季度", value: 6 },
            { name: "第三季度", value: 7 },
            { name: "第四季度", value: 8 },
          ], // 填空
          emphasis: {
            itemStyle: {
              shadowBlur: 10,
              shadowOffsetX: 0,
              shadowColor: "rgba(0, 0, 0, 0.5)",
            },
          },
        },
      ],
    };

    // var chartDom = document.getElementById("hotTeather");
    // var myChart = echarts.init(chartDom);

    // var pieDom = document.getElementById('pie');
    // var pieChart = echarts.init(pieDom);

    // this.request.get("/echarts/members").then(res => {
    //   // 填空
    //   // option.xAxis.data = res.data.x
    // option.series[0].data = res.data
    // option.series[1].data = res.data

    // option.series[2].data = [5, 6, 7, 8];
    // option.series[3].data = [5, 6, 7, 8];
    //   // 数据准备完毕之后再set
    // myChart.setOption(option);

    //   pieOption.series[0].data = [
    //     {name: "第一季度", value: res.data[0]},
    //     {name: "第二季度", value: res.data[1]},
    //     {name: "第三季度", value: res.data[2]},
    //     {name: "第四季度", value: res.data[3]},
    //   ]
    //   pieChart.setOption(pieOption)
    // })
    this.fnHotCoutse();
    this.fnHotSchool();
    this.fnHotTeather();
  },
  methods: {
    fnHotCoutse() {
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
          data: ["中大", "案板", "USA", "India", "China", "World"],
        },
        color: "#8299f2",
        series: [
          {
            type: "bar",
            data: [181203, 23489, 29034, 104970, 131744, 630230],
          },
        ],
      };
      hotCoutseChart.setOption(hotCoutseOption);
    },
    fnHotSchool() {
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
          data: ["电子", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"],
        },
        yAxis: {
          type: "value",
        },
        series: [
          {
            data: [120, 200, 150, 80, 70, 110, 130],
            type: "bar",
          },
        ],
      };
      hotSchoolChart.setOption(hotSchoolOption);
    },
    fnHotTeather() {
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
          data: ["电子", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"],
        },
        yAxis: {
          type: "value",
        },
        series: [
          {
            data: [120, 200, 150, 80, 70, 110, 130],
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
