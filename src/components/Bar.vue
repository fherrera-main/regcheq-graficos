<template>
  <Bar
    :chart-options="chartOptions"
    :chart-data="chartData"
    :chart-id="chartId"
    :dataset-id-key="datasetIdKey"
    :plugins="plugins"
    :css-classes="cssClasses"
    :styles="styles"
    :width="width"
    :height="height"
  />
</template>

<script>
import { Bar } from "vue-chartjs/legacy";

import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
} from "chart.js";

ChartJS.defaults.color = '#2b2b2b';
ChartJS.defaults.font.color = '#2b2b2b0';

ChartJS.register(
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale
);

export default {
  name: "BarChart",
  components: {
    Bar,
  },
  props: {
    chartId: {
      type: String,
      default: "bar-chart",
    },
    datasetIdKey: {
      type: String,
      default: "label",
    },
    width: {
      type: Number,
      default: 350,
    },
    height: {
      type: Number,
      default: 220,
    },
    cssClasses: {
      default: "",
      type: String,
    },
    styles: {
      type: Object,
      default: () => {},
    },
    plugins: {
      type: Array,
      default: () => [],
    },
  },
  data() {
    return {
      chartData: {
        labels: [
          "Acoso\nlaboral",
          "Acoso\nsexual",
          "Violencia en\nel trabajo",
          "Abuso de\npoder",
        ],
        datasets: [
          {
            label: "Data One",
            backgroundColor: this.getBarColors(),
            data: [46, 60, 12, 46],
            barThickness: 33,
          },
        ],
      },
      chartOptions: {
        responsive: false,
        maintainAspectRatio: false,
        scales: {
          x: {
            grid: {
              display: false,
            },
            border: {
              display: true,
              drawBorder: true,
              color:'#000'
            },
            ticks: {
              color: '#000',
              align: 'center',
              font: {
                family: 'Montserrat',
                size: 9.5,
                weight: 400,
                color: '#000'
              },
              maxRotation: 0,
              minRotation: 0,
              autoSkip: false,
              padding: 10,
              wrap: true,
              maxWidth: 150,
              callback: function(value) {
                const label = this.getLabelForValue(value);
                const maxTotalLength = 15; // Longitud máxima antes de agregar saltos
                const maxCharsPerLine = 12; // Caracteres por línea cuando se necesite dividir
                
                // Si el texto es corto, retornarlo sin modificar
                if (label.length <= maxTotalLength) {
                  return [label];
                }
                
                // Si es largo, dividirlo en líneas
                const lines = [];
                let currentLine = '';
                
                for (let i = 0; i < label.length; i++) {
                  currentLine += label[i];
                  
                  if (currentLine.length >= maxCharsPerLine && i < label.length - 1) {
                    lines.push(currentLine);
                    currentLine = '';
                  }
                }
                
                if (currentLine.length > 0) {
                  lines.push(currentLine);
                }
                
                return lines;
              }
            },
            barPercentage: 1,
            categoryPercentage: 1
          },
          y: {
            border: {
              display: false,
              drawBorder: false
            },
            ticks: {
              color: '#000',
              min: 0,
              max: 100,
              stepSize: 10,
              callback: function(value) {
                const valuesToShow = [10, 50, 100];
                console.log("GRAFICO: ",value);
                console.log("LO INCLUYE: ", valuesToShow.includes(value));
                return valuesToShow.includes(value) ? value : '';
              },
              font: {
                family: 'Montserrat',
                color: '#000',
                size: 12,
                weight: 500
              }
            },
            beginAtZero: true,
            title: {
              display: true,
              text: 'Porcentaje',
              font: {
                family: 'Montserrat',
                color: '#464555',
                size: 10,
                weight: 500
              }
            },
            grid: {
              display: true,
              drawBorder: false
            }
          }
        },
        plugins: {
          legend: {
            labels: {
              font: {
                family: 'Montserrat',
               // color: '#464555'
               weight: 500
              }
            }
          },
          tooltip: {
            titleFont: {
              family: 'Montserrat',
              //color: '#464555'
            },
            bodyFont: {
              family: 'Montserrat',
              //color: '#464555'
            }
          }
        },
        elements: {
          bar: {
            borderRadius: 6,
            borderSkipped: false,
            base: 10
          }
        },
        layout: {
          padding: {
            bottom: 0
          }
        }
      },
    };
  },
  methods: {
    getBarColors() {
      const data = [40, 80, 12, 49];
      const maxValue = Math.max(...data);
      return data.map(value => value === maxValue ? '#040045' : '#82D7EB');
    }
  },
  beforeDestroy() {
    if (this._chart) {
      this._chart.destroy();
    }
  }
};
</script>
