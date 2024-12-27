<template>
  <div class="chart-container">
    <canvas ref="pieChart"></canvas>
  </div>
</template>

<script>
import Chart from 'chart.js/auto'

export default {
  name: 'PieChart',
  props: {
    data: {
      type: Array,
      required: true,
      default: [
        { label: 'GESTOR 01', value: 25 },
        { label: 'GESTOR 02', value: 30 },
        { label: 'ADMINIST 01', value: 16 },
        { label: 'OTROS', value: 10 },
        { label: 'NO ASIGNADAS', value: 20 }
      ]
      // Expected format: [{ label: 'GESTOR 01', value: 25 }, ...]
    }
  },
  mounted() {
    this.createChart()
  },
  methods: {
    createChart() {
      const ctx = this.$refs.pieChart.getContext('2d')
      
      new Chart(ctx, {
        type: 'pie',
        data: {
          labels: this.data.map(item => item.label),
          datasets: [{
            data: this.data.map(item => item.value),
            backgroundColor: [
              '#30A6E6', // GESTOR 01 - bright blue
              '#000066', // GESTOR 02 - navy blue
              '#333333', // ADMINIST 01 - dark gray
              '#B3E0FF', // OTROS - light blue
              '#66C2E6'  // NO ASIGNADAS - medium blue
            ],
            borderWidth: 0
          }]
        },
        options: {
          responsive: true,
          maintainAspectRatio: true,
          plugins: {
            legend: {
              position: 'left',
              labels: {
                usePointStyle: true,
                padding: 20,
                font: {
                  size: 12
                }
              }
            }
          }
        }
      })
    }
  },
  watch: {
    data: {
      handler() {
        this.createChart()
      },
      deep: true
    }
  }
}
</script>

<style scoped>
.chart-container {
  position: relative;
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
}
</style>