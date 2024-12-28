<template>
  <div class="chart-container">
    <svg :width="width" :height="height" style="shape-rendering: geometricPrecision;">
      <!-- Etiqueta del eje Y -->
      <text
        x="-120"
        y="20"
        transform="rotate(-90)"
        text-anchor="middle"
        class="y-axis-title"
      >
        Porcentaje
      </text>

      <!-- Línea del eje Y -->
      <line
        x1="30"
        y1="20"
        x2="30"
        :y2="height - padding"
        stroke="#464555"
        stroke-width="1"
        shape-rendering="crispEdges"
      />
      
      <!-- Grid -->
      <template v-if="showGrid">
        <line
          v-for="tick in yTicks"
          :key="'grid-'+tick"
          x1="40"
          :x2="width - padding"
          :y1="Math.round(getYAxisPosition(tick))"
          :y2="Math.round(getYAxisPosition(tick))"
          :stroke="tick === 0 ? 'transparent' : '#E5E3F6'"
          stroke-width="0.5"
          shape-rendering="crispEdges"
        />
      </template>
      
      <!-- Barras -->
      <g v-for="(item, index) in normalizedData" :key="index">
        <!-- Barra opaca de fondo (100%) -->
        <rect
          :x="getBarCenterX(index)"
          :y="getBarY(100)"
          :width="barWidth"
          :height="getBarHeight(100)"
          fill="#F7F7FC"
          rx="10"
          ry="10"
        />
        <!-- Barra de valor actual -->
        <rect
          :x="getBarCenterX(index)"
          :y="getBarY(item)"
          :width="barWidth"
          :height="getBarHeight(item)"
          :fill="getBarColor(index)"
          rx="10"
          ry="10"
          v-b-tooltip="{
            variant: 'dark',
            placement: 'top',
            delay: { show: 100, hide: 100 }
          }"
          :title="tooltipFormat(data[index], labels[index])"
        />
        
        <!-- Labels eje X -->
        <text
          :x="getBarCenterX(index) + barWidth/2"
          :y="height - padding/2"
          text-anchor="middle"
          font-size="12"
          class="x-axis-label"
        >
          {{ labels[index] }}
        </text>
      </g>
      
      <!-- Labels eje Y -->
      <g v-for="tick in yTicks" :key="'tick-'+tick">
        <text
          x="60"
          :y="getYAxisPosition(tick) - 7"
          text-anchor="end"
          font-size="12"
          class="y-axis-label"
        >
          {{ tick }}
        </text>
      </g>
    </svg>
  </div>
</template>

<script>
export default {
  name: 'BarVariante',
  props: {
    chartData: {
      type: Array,
      default: () => [
        { value: 46, label: 'Dato 1' },
        { value: 79, label: 'Dato 2' }
      ]
    },
    width: {
      type: Number,
      default: 355
    },
    height: {
      type: Number,
      default: 250
    },
    showGrid: {
      type: Boolean,
      default: true
    },
    tooltipFormat: {
      type: Function,
      default: (value, label) => `${value}%`
    }
  },
  data() {
    return {
      padding: 40,
      initialHeight: 0
    }
  },
  computed: {
    barWidth() {
      return 25
    },
    data() {
      return this.chartData.map(item => item.value)
    },
    labels() {
      return this.chartData.map(item => item.label)
    },
    normalizedData() {
      return this.data
    },
    yTicks() {
      return [10, 50, 100]
    }
  },
  methods: {
    getBarCenterX(index) {
      const availableWidth = this.width - (this.padding * 2)
      const spacing = availableWidth / (this.data.length + 1)
      return this.padding + (spacing * (index + 1)) - (this.barWidth / 2)
    },
    getBarY(value) {
      return this.height - this.padding - this.getBarHeight(value)
    },
    getBarHeight(value) {
      const availableHeight = this.height - (this.padding * 2)
      return (availableHeight * value * this.initialHeight) / 100
    },
    getYAxisPosition(value) {
      const availableHeight = this.height - (this.padding * 2)
      return this.height - this.padding - ((availableHeight * value) / 100)
    },
    getBarColor(index) {
      return ['#52B4D2', '#0084C7'][index]
    }
  },
  mounted() {
    setTimeout(() => {
      this.initialHeight = 1
    }, 100)
  }
}
</script>

<style scoped>
.chart-container {
  position: relative;
  font-family: 'Montserrat', sans-serif;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
}

rect {
  transition: all 0.5s ease;
}

rect:hover {
  opacity: 0.8;
}

.x-axis-label {
  font-weight: 500;
  color: #242432;
  font-size: 12px;
}

.y-axis-label {
  font-size: 12px;
  font-weight: 400;
  fill: #464555;
}

.y-axis-title {
  font-size: 10px;
  font-weight: 500;
  fill: #464555;
}
/* Agregar estos estilos globales */
.tooltip.b-tooltip {
  font-family: 'Montserrat', sans-serif;
  font-size: 12px;
  font-weight: 300;
  line-height: 14.63px;
  letter-spacing: 0.0025em;
}

.tooltip.b-tooltip .tooltip-inner {
  background-color: #464555;
}

.tooltip.b-tooltip.bs-tooltip-top .arrow::before {
  border-top-color: #464555;
}
</style>
