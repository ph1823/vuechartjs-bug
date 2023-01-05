<template>
  <div>
    <div class="row">
      <div class="col-lg-6 col-md-6">
        <div class="d-new-flex m-b-10 no-block">
          <h5 class="card-title m-b-0 align-self-center text-uppercase">Statistiques</h5>
        </div>
        <ul class="list-inline m-b-10 text-uppercase lp-5 font-medium font-12">
          <li @click="hide('CPU')" id="cpu-stats" :style="'text-decoration: ' + this.style['CPU']"><i class="fa fa-square m-r-5 text-warning"></i> CPU (en %)</li>
          <li @click="hide('RAM')" id="ram-stats" :style="'text-decoration: ' + this.style['RAM']"><i class="fa fa-square m-r-5 text-danger"></i> RAM (en Mo)</li>
          <li @click="hide('DISK')" id="disk-stats" :style="'text-decoration: ' + this.style['DISK']"><i class="fa fa-square m-r-5"
                                                                                                         style="color: #03e5fa;"></i> Disque (en Mo)
          </li>
        </ul>
      </div>
    </div>
    <div class="clearfix"></div>
    <div style="height: 400px; width: 100%">
      <Line :data="chartData"
            :options="options" ref="stats"/>
      <div class="clearfix"></div>
    </div>
  </div>
</template>
<script>
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  LineElement,
  PointElement,
  Title,
  Tooltip,
  Legend,
  TimeSeriesScale
} from 'chart.js';

import { Line } from 'vue-chartjs';
import 'chartjs-adapter-moment';
ChartJS.register(
    CategoryScale,
    LinearScale,
    TimeSeriesScale,
    LineElement,
    PointElement,
    Title,
    Tooltip,
    Legend
);
export default {
  name: 'Chart',
  props: ["optionsAdd"],
  components: { Line },
  data() {
    return {
      style:  {
        RAM: "none",
        DISK: "none",
        CPU: "none"
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        hoverMode: 'index',
        plugins: {
          legend: {
            display: false
          }
        },
        scales: {
          x: {
            display: true,
            type: 'timeseries',
            bounds: 'data',
            time: {
              unit: 'second',
              displayFormats: {
                second: "hh:mm:ss"
              }
            },
            grid: {
              display: false,
              drawTicks: false,
            },
            ticks: {
              source: "labels"
            }
          },
          yRD: {
            type: 'linear',
            ticks: {
              beginAtZero: true
            },
            position: 'left',
            min: 0,
            title: {
              display: true,
              labelString: 'RAM (en Mo) et Disque (Mo)'
            }
          },
          yCPU: {
            type: 'linear',
            ticks: {
              beginAtZero: true
            },
            max: 100,
            min: 0,
            position: 'right',
            grid: {
              drawOnChartArea: false
            },
            title: {
              display: true,
              labelString: 'CPU (en %)'
            }
          }
        }
      },
      cData: [0, 0, 0]
    }
  },
  computed: {
    chartData: {
      // getter
      get() {
        return {
          labels: [new Date(Date.now() - 30000), new Date(Date.now() - 60000), new Date(Date.now() - 90000)],
          datasets: [
            {
              backgroundColor: "#FF484C",
              borderColor: "#FF484C",
              fill: false,
              label: "RAM",
              data: this.cData,
              yAxisID: "yRD"
            },
            {
              backgroundColor: "#FA7D03",
              borderColor: "#FA7D03",
              fill: false,
              label: "CPU",
              data: this.cData,
              yAxisID: "yCPU"
            },
            {
              backgroundColor: "#03e5fa",
              borderColor: "#03e5fa",
              fill: false,
              label: "Disque",
              yAxisID: "yRD",
              data: this.cData
            }
          ]
        }
      }
    }
  },
  mounted() {
    const instance = this;
    /*this.chartData.datasets.push({
      data: [0, 1, 3]
    })
    The code on up work to add new data
    Code down generate error
    */
    this.chartData.labels.push(Date.now());
    console.log(this.cData);
    this.cData.push(0);
    console.log(this.cData);
  },
  methods: {
    hide: function(type) {
      let obj = {RAM: 0, CPU: 1, DISK: 2};
      if(this.$refs.stats.chart.getDataVisibility(obj[type])) {
        this.$refs.stats.chart.hide(obj[type]);
        this.style[type] = "line-through";
      } else {
        this.$refs.stats.chart.show(obj[type]);
        this.style[type] = "none";
      }
      this.$refs.stats.chart.toggleDataVisibility(obj[type]);
      this.$refs.stats.chart.update();
    },
  }
}
</script>