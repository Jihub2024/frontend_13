<template>
  <div style="position: relative; width: 100%; height: 100%">
    <div
        id="burnup-chart"
        style="position: absolute; width: 80%; height: 40%; left: 10%; top: 5%"
    >
      燃尽图
    </div>
    <div
        ref="chart"
        style="position: absolute; width: 80%; height: 40%; left: 10%; top: 52%"
    >
      进度显示图
    </div>
    <div style="position: absolute; width: 80%; height: 5%; left: 10%; top: 95%">
      <h1>协作关系图</h1>

    </div>
    <div
        ref="chartC1"
        style="position: absolute; width: 80%; height: 100%; left: 10%; top: 100%"
    >
      协作关系图
    </div>

    <v-btn class="ma-2" :color="getTopicColor(user.topic)" @click="back">
      <v-icon dark left> mdi-arrow-left</v-icon>
      返回
    </v-btn>
  </div>
</template>

<script>
import * as echarts from "echarts";
import topicSetting from "@/utils/topic-setting";
import {getActivations, showPersonList, showTaskList} from "@/api/user";

export default {

  data() {
    return {
      chartData: {
        workloads: [], // 工作量
        expectedDates: [], // 预期完成日期
        actualDates: [], // 实际完成日期
        dates: [], // 日期
        resWorkloads: [],

      },
      allPeople: [],
      tasks: [],
      webkitDep: {
        type: "force",
        categories: [],
        nodes: [],
        links: [],
      },
      act:{},
    };
  },
  mounted() {
    this.drawP();
    this.drawB();
    this.drawC();
  },
  inject: {
    user: {default: null},
    'selectedProj': {default: null}
  },
  methods: {
    transform(state) {
      if (state === 'A') {
        return '已完成';
      } else if (state === 'B') {
        return '进行中';
      } else if (state === 'C') {
        return '未开始';
      } else if (state === 'D') {
        return '不合法';
      }
    },
    getColor(state) {
      if (state === "A") {
        return "green";
      } else if (state === "B") {
        return "orange";
      } else if (state === "C") {
        return "blue";
      } else if (state === "D") {
        return "red";
      }
    },
    drawP() {
      console.log(echarts) // Don't delete this. This is to avoid build errors
      var chart1 = echarts.init(this.$refs.chart);
      window.addEventListener("resize", function () {
        chart1.resize();
      });

      let projectItem = this.$route.query.projectItem; //每个任务的名称
      let projectItemStart = this.$route.query.projectItemStart;//每个任务的启动时间
      let projectItemEnd = this.$route.query.projectItemEnd; //每个任务的结束时间
      let projectState = this.$route.query.projectState;

      //   let projectItem = ["1", "2"]; //每个任务的名称
      //   let projectItemStart = ["2022-5-24", "2022-5-27"]; //每个任务的启动时间
      //   let projectItemEnd = ["2022-5-27", "2022-5-29"]; //每个任务的结束时间
      //   let projectState = ["A", "B"];

      let projectItemStartValue = projectItemStart.map((item) => {
        return new Date(item).valueOf();
      });
      console.log(projectItemStartValue);
      let projectItemDuringValue = projectItemEnd.map((item, i) => {
        return new Date(item).valueOf() - projectItemStartValue[i];
      });
      console.log(projectItemDuringValue);
      let dateMin = projectItemStartValue[0];
      for (let i = 0; i < projectItemStartValue.length; i++) {
        if (projectItemStartValue[i] < dateMin) {
          dateMin = projectItemStartValue[i];
        }
      }
      console.log(dateMin);
      var option = {
        title: {
          text: "任务进度显示图",
        },
        tooltip: {
          trigger: "axis",
          formatter: function (params) {
            var tar = params[1];
            let state = projectState[tar.dataIndex];
            let startTime = projectItemStartValue[tar.dataIndex];
            let endTime = projectItemStartValue[tar.dataIndex] + projectItemDuringValue[tar.dataIndex];
            var s;
            if (state === 'A') {
              s = '已完成';
            } else if (state === 'B') {
              s = '进行中';
            } else if (state === 'C') {
              s = '未开始';
            } else if (state === 'D') {
              s = '延期已完成';
            } else if (state === 'E') {
              s = '延期未完成';
            }
            return (
                tar.name +
                "<br/>" +
                tar.seriesName +
                " : " + new Date(startTime).getFullYear() + "-" + (new Date(startTime).getMonth() + 1) + "-" + new Date(startTime).getDate()
                + "-->" + new Date(endTime).getFullYear() + "-" + (new Date(startTime).getMonth() + 1) + "-" + new Date(endTime).getDate() +
                "<br/>" + "状态：" + s
            );
          },
        },
        grid: {
          left: "3%",
          right: "4%",
          bottom: "30%",
          containLabel: true,
        },
        yAxis: {
          type: "category",
          splitLine: {
            show: false,
          },
          data: projectItem,
        },
        xAxis: {
          type: "value",
          min: dateMin,
          axisLabel: {
            color: "#333", // 坐标轴文字颜色
            formatter: function (param) {
              let date = new Date(param);
              let dateLabel =
                  date.getFullYear() +
                  "-" +
                  (date.getMonth() + 1) +
                  "-" +
                  date.getDate();
              return dateLabel;
            },
          },
        },
        series: [
          {
            name: "Placeholder",
            type: "bar",
            stack: "Total",
            itemStyle: {
              borderColor: "transparent",
              color: "transparent",
            },
            emphasis: {
              itemStyle: {
                borderColor: "transparent",
                color: "transparent",
              },
            },
            data: projectItemStartValue,
          },
          {
            name: "周期",
            type: "bar",
            stack: "Total",
            itemStyle: {
              normal: {
                color: function (params) {
                  let state = projectState[params.dataIndex];
                  if (state === 'A') {
                    return 'green';
                  } else if (state === 'B') {
                    return 'orange';
                  } else if (state === 'C') {
                    return 'blue';
                  } else if (state === 'D') {
                    return 'red';
                  } else if (state === 'E') {
                    return 'yellow';
                  }
                },
              }
            },
            data: projectItemDuringValue,
          },
        ],
      };
      // 使用刚指定的配置项和数据显示图表。
      chart1.setOption(option);
    },
    drawB() {
      // 模拟数据
      this.chartData.workloads = this.$route.query.workloads;
      this.chartData.expectedDates = this.$route.query.expectedDates;

      for (let i = 0; i < this.chartData.workloads.length; i++) {
        this.chartData.workloads[i] = parseInt(this.chartData.workloads[i]);
      }

      for (let i = 0; i < this.$route.query.actualDates.length; i++) {
        if (this.$route.query.actualDates[i] !== "2050-12-31") {
          this.chartData.actualDates.push(this.$route.query.actualDates[i]);
        }
      }

      let sum = 0;
      for (let i = 0; i < this.chartData.workloads.length; i++) {
        sum += this.chartData.workloads[i];
      }
      console.log(sum);

      let expectedDatesValue = this.chartData.expectedDates.map((item) => {
        return new Date(item).valueOf();
      });

      let notSortDatesValue = [];
      for (let i = 0; i < expectedDatesValue.length; i++) {
        notSortDatesValue.push(expectedDatesValue[i]);
      }

      expectedDatesValue.sort()[0];

      var newArr = new Set(expectedDatesValue);
      var timeArr = Array.from(newArr);

      let resWorkloadsE = [];
      let res = sum;
      for (let i = 0; i < timeArr.length; i++) {
        for (let j = 0; j < notSortDatesValue.length; j++) {
          if (notSortDatesValue[j] == timeArr[i]) {
            res -= this.chartData.workloads[j];
          }
        }
        resWorkloadsE.push([timeArr[i], res]);
      }

      console.log(resWorkloadsE);

      for (let i = 0; i < this.chartData.actualDates.length; i++) {
        if (this.chartData.actualDates[i] == "") {
          this.chartData.actualDates.splice(i, 1);
        }
      }

      let actualDatesValue = this.chartData.actualDates.map((item) => {
        return new Date(item).valueOf();
      });

      let notSortADatesValue = [];
      for (let i = 0; i < actualDatesValue.length; i++) {
        notSortADatesValue.push(actualDatesValue[i]);
      }

      actualDatesValue.sort()[0];

      var newArr1 = new Set(actualDatesValue);
      var timeArr1 = Array.from(newArr1);

      let resWorkloadsA = [];
      // console.log(notSortDatesValue);
      // console.log(timeArr);
      res = sum;
      for (let i = 0; i < timeArr1.length; i++) {
        for (let j = 0; j < notSortADatesValue.length; j++) {
          if (notSortADatesValue[j] == timeArr1[i]) {
            res -= this.chartData.workloads[j];
          }
        }
        // resWorkloadsA[timeArr1[i]] = res;
        resWorkloadsA.push([timeArr1[i], res]);
      }
      console.log(resWorkloadsA);

      let dateMin;
      if (actualDatesValue.length == 0) {
        dateMin = expectedDatesValue[0];
      } else {
        dateMin =
            expectedDatesValue[0] < actualDatesValue[0]
                ? expectedDatesValue[0]
                : actualDatesValue[0];
      }
      dateMin = dateMin - 24 * 60 * 60 * 1000;
      resWorkloadsA.push([dateMin, sum]);
      resWorkloadsE.push([dateMin, sum]);

      function sortByField(x, y) {
        return x[0] - y[0];
      }

      resWorkloadsA.sort(sortByField);
      resWorkloadsE.sort(sortByField);

      // 绘制燃尽图
      console.log(echarts) // Don't delete this. This is to avoid build errors
      const chart2 = echarts.init(document.getElementById("burnup-chart"));
      window.addEventListener("resize", function () {
        chart2.resize();
      });

      chart2.setOption({
        title: {
          text: "燃尽图",
        },
        legend: {
          data: ["预期中的剩余工作量", "实际的剩余工作量"],
          y: "bottom",
          x: "center",
        },
        tooltip: {
          trigger: "axis",
          formatter: function (params) {
            return params[0]["seriesName"] + ":" + params[0]["value"][1];
          },
        },
        xAxis: {
          type: "value",
          min: dateMin,
          axisLabel: {
            color: "#333", // 坐标轴文字颜色
            formatter: function (param) {
              let date = new Date(param);
              let dateLabel =
                  date.getFullYear() +
                  "-" +
                  (date.getMonth() + 1) +
                  "-" +
                  date.getDate();
              return dateLabel;
            },
          },
        },
        yAxis: {
          type: "value",
        },
        series: [
          {
            name: "预期中的剩余工作量",
            type: "line",
            data: resWorkloadsE,
          },
          {
            name: "实际的剩余工作量",
            type: "line",
            data: resWorkloadsA,
          },
        ],
      });
    },
    drawC() {

      getActivations({project_id: this.selectedProj.projectId}).then(
          res=>{
            console.log("fff" + JSON.stringify(res['data']))
      var act = res.data.data;



      var dom = this.$refs.chartC1;
      var myChart = echarts.init(dom, null, {
        renderer: 'canvas',
        useDirtyRect: false
      });
      myChart.showLoading();
      //绘制协作图
      showPersonList({projectId: this.selectedProj.projectId, userId: this.user.id}).then(
          res => {
            this.allPeople = res['data']['data'];
            showTaskList({userId: this.user.id, projectId: this.selectedProj.projectId}).then(
                res => {
                  myChart.hideLoading();
                  this.tasks = res['data']['data'];
                  this.webkitDep.categories.push('people');
                  var names=[];
                  for (let i = 0; i < this.allPeople.length; i++) {
                    names.push(this.allPeople[i].peopleName);
                    this.webkitDep.nodes.push({
                      name: "项目成员\n" + this.allPeople[i].peopleName + "\n活跃度为：" + (act[this.allPeople[i].peopleId]!=null ? act[this.allPeople[i].peopleId] : 50),
                      value: 1,
                      category: 0,
                      symbolSize: (act[this.allPeople[i].peopleId]!=null ? act[this.allPeople[i].peopleId] : 50),
                    })
                  }


                  for (let i = 0; i < this.tasks.length; i++) {
                    this.webkitDep.categories.push(this.tasks[i].taskName);
                    this.webkitDep.nodes.push({
                      name: "冲刺名称：" + this.tasks[i].taskName,
                      value: 10,
                      category: i+1,
                      symbolSize: 100,
                    });
                    const targetIndex = this.webkitDep.nodes.length - 1;
                    for (let j = 0; j < this.tasks[i].subTaskList.length; j++) {
                      this.webkitDep.nodes.push({
                        name: "任务" + this.tasks[i].subTaskList[j].subTaskName,
                        value: 1,
                        category: i+1,
                        symbolSize: 50,
                      });
                      this.webkitDep.links.push({
                        source: targetIndex + j + 1,
                        target: targetIndex
                      });
                      var target_p_id = 0;
                      for (let k = 0; k < this.allPeople.length; k++) {
                        if(this.allPeople[k].peopleId === this.tasks[i].subTaskList[j].managerId){
                          var target_p_name = this.allPeople[k].peopleName;
                          target_p_id = names.indexOf(target_p_name);
                          break;
                        }
                      }
                      this.webkitDep.links.push({
                        source: targetIndex + j + 1,
                        target: target_p_id
                      });
                    }
                  }

                  console.log(this.webkitDep);

                  var option;
                  option = {
                    legend: {
                      data: this.webkitDep.categories,
                      show: true,
                    },
                    series: [
                      {
                        type: 'graph',
                        layout: 'force',
                        animation: false,
                        roam: true,
                        label: {
                          show: true,
                        },
                        draggable: true,
                        data: this.webkitDep.nodes.map(function (node, idx) {
                          node.id = idx;
                          return node;
                        }),
                        categories: this.webkitDep.categories,
                        force: {
                          edgeLength: 200,
                          repulsion: 1000,
                          gravity: 0.2
                        },
                        edges: this.webkitDep.links
                      }
                    ]
                  };
                  myChart.setOption(option);
                  if (option && typeof option === 'object') {
                    myChart.setOption(option);
                  }
                }
            );
          }

      );
      })


    },
    back() {
      this.$router.go(-1);
    },
    getTopicColor: topicSetting.getColor
  },
  created() {
    showPersonList({projectId: this.selectedProj.projectId, userId: this.user.id}).then(
        res => {
          this.allPeople = res['data']['data'];
        }
    );
  }
};
</script>
