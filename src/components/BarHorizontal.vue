<template>
  <div class="chart-container">
    <svg :width="width" :height="totalHeight" style="shape-rendering: geometricPrecision;">
      <!-- Grid vertical (líneas de porcentaje) -->
      <template v-if="showGrid">
        <line
          v-for="tick in xTicks"
          :key="'grid-'+tick"
          :x1="getXAxisPosition(tick)"
          :x2="getXAxisPosition(tick)"
          :y1="padding"
          :y2="barsHeight + padding"
          :stroke="tick === 0 ? '#464555' : '#E5E3F6'"
          stroke-width="0.5"
          shape-rendering="crispEdges"
        />
      </template>

      <!-- Barras horizontales -->
      <g v-for="(item, index) in normalizedData" :key="index">
        <path
          :d="getBarPath(index, item)"
          :fill="getBarColor(index)"
        />

        <!-- Labels sobre las barras -->
        <text
          :x="getBarLabelX(item)"
          :y="getBarY(index) + barHeight/2"
          text-anchor="start"
          dominant-baseline="middle"
          font-size="12"
          class="bar-label"
          fill="white"
        >
          {{ labels[index] }}
        </text>
      </g>

      <!-- Labels eje X (porcentajes) -->
      <g v-for="tick in xTicks" :key="'x-tick-'+tick">
        <text
          :x="getXAxisPosition(tick)"
          :y="height - 20"
          text-anchor="end"
          font-size="12"
          class="x-axis-label"
        >
          {{ formatXAxis(tick) }}
        </text>
      </g>
    </svg>
  </div>
</template>

<script>
export default {
  name: 'BarHorizontal',
  props: {
    chartData: {
      type: Array,
      required: true,
      validator: value => value.every(item => 
        'value' in item && 'label' in item
      )
    },
    width: {
      type: Number,
      default: 300
    },
    height: {
      type: Number,
      default: 400
    },
    colors: {
      type: Array,
      default: () => ['#040045', '#36336A', '#004DC0', '#0064E0', '#358DFF']
    },
    showGrid: {
      type: Boolean,
      default: true
    }
  },
  data() {
    return {
      padding: 60
    }
  },
  computed: {
    data() {
      return this.chartData.map(item => item.value)
    },
    labels() {
      return this.chartData.map(item => item.label)
    },
    barHeight() {
      const availableHeight = this.height - this.padding * 2
      return Math.min(40, availableHeight / this.data.length)
    },
    maxValue() {
      return Math.max(...this.data)
    },
    normalizedData() {
      return this.data.map(value => value / this.maxValue)
    },
    xTicks() {
      const maxValue = Math.max(...this.data);
      const ticks = [10]; // Comenzamos con 0 y 10
      
      // Agregamos valores de 50 en 50 hasta superar el valor máximo
      let currentValue = 50;
      while (currentValue <= maxValue) {
        ticks.push(currentValue);
        currentValue += 50;
      }
      
      // Si el último valor es menor que el máximo, agregamos un tick adicional
      if (ticks[ticks.length - 1] < maxValue) {
        ticks.push(currentValue);
      }
      
      return ticks;
    },
    barsHeight() {
      // Altura exacta ocupada por todas las barras
      return this.data.length * this.barHeight
    },
    totalHeight() {
      // Altura total del SVG incluyendo padding para labels
      return this.barsHeight + this.padding * 2
    },
    effectiveHeight() {
      return this.data.length * this.barHeight + this.padding
    }
  },
  methods: {
    getBarY(index) {
      return index * this.barHeight + this.padding
    },
    getBarWidth(normalizedValue) {
      return normalizedValue * (this.width - this.padding * 2)
    },
    getBarColor(index) {
      return this.colors[index % this.colors.length]
    },
    getXAxisPosition(tick) {
      return (tick / 100) * (this.width - this.padding * 2) + this.padding
    },
    formatXAxis(value) {
      return new Intl.NumberFormat('es-ES', {
        minimumFractionDigits: 0,
        maximumFractionDigits: 0
      }).format(value)
    },
    getRoundedCorners(index) {
      if (index === 0) {
        // Primera barra: solo esquina superior derecha redondeada
        return {
          rx: '8',
          ry: '8'
        }
      } else {
        // Resto de barras: esquina inferior derecha redondeada
        return {
          rx: '8',
          ry: '8'
        }
      }
    },
    getBarPath(index, normalizedValue) {
      const x = this.getXAxisPosition(0);
      const y = this.getBarY(index);
      const width = this.getBarWidth(normalizedValue);
      const height = this.barHeight;
      const radius = 8;

      if (index === 0) {
        // Primera barra: solo esquina superior derecha redondeada
        return `
          M ${x},${y}
          L ${x + width - radius},${y}
          Q ${x + width},${y} ${x + width},${y + radius}
          L ${x + width},${y + height}
          L ${x},${y + height}
          Z
        `;
      } else {
        // Resto de barras: solo esquina inferior derecha redondeada
        return `
          M ${x},${y}
          L ${x + width},${y}
          L ${x + width},${y + height - radius}
          Q ${x + width},${y + height} ${x + width - radius},${y + height}
          L ${x},${y + height}
          Z
        `;
      }
    },
    getBarLabelX(normalizedValue) {
      // Posicionamos el texto a 10px del inicio de la barra
      return this.padding + 12
    }
  }
}
</script>

<style scoped>
.chart-container {
  font-family: 'Montserrat', sans-serif;
}

.x-axis-label {
  fill: #464555;
  font-weight: 400;
}

.y-axis-label {
  fill: #464555;
  font-weight: 400;
}

.bar-label {
  font-family: 'Montserrat', sans-serif;
  font-weight: 400;
}
</style>

