<template>
  <div class="chart-container">
    <svg :width="width" :height="height" style="shape-rendering: geometricPrecision;">
      <!-- Grid -->
      <template v-if="showGrid">
        <line
          v-for="tick in yTicks"
          :key="'grid-'+tick"
          x1="40"
          :x2="width - padding"
          :y1="Math.round(getYAxisPosition(tick))"
          :y2="Math.round(getYAxisPosition(tick))"a
          :stroke="tick === Math.min(...yTicks) ? '#464555' : '#E5E3F6'"
          stroke-width="0.5"
          shape-rendering="crispEdges"
          vector-effect="non-scaling-stroke"
        />
      </template>
          
      <!-- Barras -->
      <g v-for="(item, index) in normalizedData" :key="index">
        <rect
          :x="getBarCenterX(index)"
          :y="getBarY(item)"
          :width="barWidth"
          :height="getBarHeight(item)"
          :fill="getBarColor(index)"
          rx="6"
          ry="6"
          v-b-tooltip="{
            variant: 'dark',
            placement: 'top',
            delay: { show: 100, hide: 100 }
          }"
          :title="tooltipFormat(data[index], labels[index])"
        />
        
        <!-- Labels eje X -->
        <g v-for="(line, lineIndex) in wrapLabel(labels[index])" :key="`${index}-${lineIndex}`">
          <text
            :x="getBarX(index) + labelContainerWidth/2"
            :y="height - padding + 20 + (lineIndex * 14)"
            text-anchor="middle"
            font-size="12"
            class="x-axis-label"
          >
            {{ line }}
          </text>
        </g>
      </g>
      
      <!-- Labels eje Y -->
      <g v-for="(tick, index) in yTicks" :key="'tick-'+index">
        <text
          v-if="tick !== 0"
          x="60"
          :y="getYAxisPosition(tick) - 7"
          text-anchor="end"
          font-size="12"
          class="y-axis-label"
        >
          {{ formatYAxis(tick) }}
        </text>
      </g>

      <!-- Título del eje Y -->
      <text
        x="30"
        :y="height/2"
        text-anchor="middle"
        :transform="`rotate(-90 20 ${height/2})`"
        class="axis-title"
      >
        {{ yAxisTitle }}
      </text>

    </svg>
  </div>
</template>

<script>
export default {
  name: 'BarTest',
  props: {
    chartData: {
      type: Array,
      default: () => [
        { value: 46, label: 'Acoso laboral' },
        { value: 79, label: 'Acoso sexual' },
        { value: 20, label: 'Violencia en el trabajo' },
        { value: 46, label: 'Abuso de poder' }
      ]
    },
    width: {
      type: Number,
      default: 500
    },
    height: {
      type: Number,
      default: 250
    },
    barColor: {
      type: String,
      default: null
    },
    showGrid: {
      type: Boolean,
      default: true
    },
    tooltipFormat: {
      type: Function,
      default: (value, label) => `${value} denuncias`
    },
    formatYAxis: {
      type: Function,
      default: value => value
    },
    yAxisTitle: {
      type: String,
      default: 'Porcentaje'
    }
  },
  data() {
    return {
      padding: 40,
      colors: ['#FF6384', '#36A2EB', '#FFCE56', '#4BC0C0', '#9966FF'],
      initialHeight: 0
    }
  },
  computed: {
    barWidth() {
      return 33  // Ancho de la barra
    },
    labelContainerWidth() {
      return 100      // Ancho del contenedor para el texto (puedes ajustar este valor)
    },
    data() {
      return this.chartData.map(item => item.value);
    },
    labels() {
      return this.chartData.map(item => item.label);
    },
    normalizedData() {
      return this.data;
    },
    maxValueIndex() {
      return this.data.indexOf(Math.max(...this.data))
    },
    yTicks() {
      const maxValue = Math.max(...this.data);
      const ticks = [0, 10]; // Comenzamos con 0 y 10
      
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
    }
  },
  methods: {
    getBarX(index) {
      return 65 + (index * (this.labelContainerWidth - 20));
    },
    getBarCenterX(index) {
      // Centramos la barra en su contenedor
      const barX = this.getBarX(index);
      return barX + (this.labelContainerWidth - this.barWidth) / 2;
    },
    getBarY(value) {
      return this.height - this.padding - this.getBarHeight(value);
    },
    getBarHeight(value) {
      const maxTick = Math.max(...this.yTicks);
      const availableHeight = this.height - (this.padding * 2);
      // Calculamos la altura proporcional basada en el valor máximo de los ticks
      return (availableHeight * value * this.initialHeight) / maxTick;
    },
    getYAxisPosition(value) {
      const maxTick = Math.max(...this.yTicks);
      const availableHeight = this.height - (this.padding * 2);
      return this.height - this.padding - ((availableHeight * value) / maxTick);
    },
    wrapLabel(text) {
      const maxCharsPerLine = Math.floor(this.labelContainerWidth / 7); // Aproximadamente 7px por caracter
      const words = text.split(' ');
      const lines = [];
      let currentLine = '';

      words.forEach(word => {
        if ((currentLine + ' ' + word).length <= maxCharsPerLine) {
          currentLine += (currentLine ? ' ' : '') + word;
        } else {
          lines.push(currentLine);
          currentLine = word;
        }
      });
      
      if (currentLine) {
        lines.push(currentLine);
      }
      
      return lines;
    },
    getBarColor(index) {
      return index === this.maxValueIndex ? '#040045' : '#82D7EB'
    }
  },
  mounted() {
    // Iniciamos la animación después de que el componente se monta
    setTimeout(() => {
      this.initialHeight = 1;
    }, 100);
  }
}
</script>

<style scoped>
* {
    font-family: 'Montserrat', sans-serif;
}
.chart-container {
  position: relative;
  font-family: 'Montserrat', sans-serif;
  width: 100%;
  height: 100%;
}

rect {
  transition: all 0.5s ease;
}

rect:hover {
  opacity: 0.8;
}

.x-axis-label {
  /* Estilos para labels del eje X */
  font-weight: 400;
  color: #000;
  font-size: 10px;
  /* Añade más estilos según necesites */
}

.y-axis-label {
  /* Estilos para labels del eje Y */
  font-size: 12px;
  font-weight: 400;
  color: #464555;
  /* Añade más estilos según necesites */
}

.axis-title {
  font-weight: 400;
  fill: #464555;
  font-size: 10px;
  font-weight: 500;
  line-height: 12.19px;
  letter-spacing: 0.0025em;
  text-align: center;
  text-underline-position: from-font;
  text-decoration-skip-ink: none;

}
</style>

