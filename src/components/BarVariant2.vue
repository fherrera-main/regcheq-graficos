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
          :y2="Math.round(getYAxisPosition(tick))"
          stroke="#E5E3F6"
          stroke-width="0.5"
          shape-rendering="crispEdges"
        />
      </template>
          
      <!-- Rectángulo base para labels eje X -->
      <rect
        class="background-rect"
        x="40"
        :y="height - paddingBottom - 7"
        :width="width - padding - 40"
        height="50"
        fill="#F7F7FC"
        opacity="1" 
        rx="10"
        ry="10"
      />
      
      <!-- Línea del eje X -->
      <line
        :x1="57 + 20"
        :x2="width - padding - 40"
        :y1="height - paddingBottom + 52"
        :y2="height - paddingBottom + 52"
        stroke="#464555"
        stroke-width="0.4"
        shape-rendering="crispEdges"
      />
      
      <!-- Título del eje X -->
      <text
        :x="40 + (width - padding - 40) / 2"
        :y="height - paddingBottom + 70"
        text-anchor="middle"
        class="x-axis-title"
      >
        Cantidad de días sin actualización
      </text>
      
      <!-- Barras -->
      <g v-for="(item, index) in chartData" :key="index">
        <!-- Barra completa con clip-path -->
        <g :clip-path="`url(#clip-${index})`">
          <!-- Definición del clip-path -->
          <clipPath :id="`clip-${index}`">
            <rect 
              :x="getBarCenterX(index)" 
              :y="getBarY(item.nuevas + item.enCurso)"
              :width="barWidth" 
              :height="getBarHeight(item.nuevas + item.enCurso)"
              :rx="shouldHaveRoundedCorners(item.nuevas, item.enCurso) ? 20 : 8"
            />
          </clipPath>

          <!-- Barra superior (en curso) -->
          <rect
            :x="Math.round(getBarCenterX(index))"
            :y="Math.round(getBarY(item.nuevas + item.enCurso))"
            :width="barWidth"
            :height="Math.round(getBarHeight(item.nuevas + item.enCurso))"
            fill="#52B4D2"
            shape-rendering="crispEdges"
            v-b-tooltip="{
              variant: 'dark',
              placement: 'top',
              delay: { show: 100, hide: 100 }
            }"
            :title="`En curso: ${item.enCurso}`"
          />
          
          <!-- Barra inferior (nuevas) -->
          <path
            :d="getBarPath(index, item)"
            fill="#0084C7"
            v-b-tooltip="{
              variant: 'dark',
              placement: 'top',
              delay: { show: 100, hide: 100 }
            }"
            :title="`Nuevas: ${item.nuevas}`"
          />
        </g>
        
        <!-- Labels eje X -->
        <g v-for="(line, lineIndex) in wrapLabel(item.rango)" :key="`${index}-${lineIndex}`">
          <text
            :x="getBarX(index) + labelContainerWidth/2"
            :y="height - paddingBottom + 15 + (lineIndex * 14)"
            text-anchor="middle"
            font-size="12"
            class="x-axis-label"
          >
            {{ line }}
          </text>
        </g>
      </g>
      
      <!-- Labels eje Y -->
      <g v-for="tick in yTicks" :key="'tick-'+tick">
        <text
          x="60"
          :y="getYAxisPosition(tick) - 5"
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
  name: 'BarVariant2',
  props: {
    chartData: {
      type: Array,
      default: () => [
        { rango: 'Menos de 1 día', nuevas: 47, enCurso: 15 },
        { rango: 'Entre 1 y 3 días', nuevas: 20, enCurso: 25 },
        { rango: 'Entre 3 y 7 días', nuevas: 15, enCurso: 20 },
        { rango: 'Más de una semana', nuevas: 30, enCurso: 35 }
      ]
    },
    width: {
      type: Number,
      default: 400
    },
    height: {
      type: Number,
      default: 350
    },
    showGrid: {
      type: Boolean,
      default: true
    },
    yAxisTitle: {
      type: String,
      default: 'Porcentaje'
    }
  },
  data() {
    return {
      padding: 50,
      paddingBottom: 90,
      initialHeight: 0
    }
  },
  computed: {
    barWidth() {
      return 39
    },
    labelContainerWidth() {
      return 88.9
    },
    yTicks() {
      return [10, 50, 100]
    }
  },
  methods: {
    getBarX(index) {
      return 48 + (index * (this.labelContainerWidth - 20))
    },
    getBarCenterX(index) {
      const barX = this.getBarX(index)
      return barX + (this.labelContainerWidth - this.barWidth) / 2
    },
    getBarY(value) {
      return this.height - this.paddingBottom - this.getBarHeight(value)
    },
    getBarHeight(value) {
      const maxValue = 100
      const scale = value >= 50 ? 
        (value - 50) / 50 * 87 + 87 :
        value / 50 * 87
      
      return scale
    },
    getYAxisPosition(value) {
      const maxValue = 100
      const scale = value >= 50 ? 
        (value - 50) / 50 * 87 + 87 :
        value / 50 * 87
      
      return this.height - this.paddingBottom - scale
    },
    wrapLabel(text) {
      const maxCharsPerLine = Math.floor(this.labelContainerWidth / 8)
      const words = text.split(' ')
      const lines = []
      let currentLine = ''

      words.forEach(word => {
        if ((currentLine + ' ' + word).length <= maxCharsPerLine) {
          currentLine += (currentLine ? ' ' : '') + word
        } else {
          lines.push(currentLine)
          currentLine = word
        }
      })
      
      if (currentLine) {
        lines.push(currentLine)
      }
      
      return lines
    },
    formatYAxis(value) {
      return value
    },
    shouldHaveRoundedCorners(nuevas, enCurso) {
      return nuevas > 5 || enCurso > 5;
    },
    getBarPath(index, item) {
      const x = this.getBarCenterX(index);
      const y = this.getBarY(item.nuevas);
      const width = this.barWidth;
      const height = this.getBarHeight(item.nuevas);
      
      if (!this.shouldHaveRoundedCorners(item.nuevas, item.enCurso)) {
        return `M${x} ${y} H${x + width} V${y + height} H${x} Z`;
      } else {
        const radius = 20;
        return `
          M${x} ${y}
          H${x + width}
          V${y + height}
          C${x + width} ${y + height + radius} ${x} ${y + height + radius} ${x} ${y + height}
          Z
        `;
      }
    }
  }
}
</script>

<style scoped>
.chart-container {
  position: relative;
  font-family: 'Montserrat', sans-serif;
  width: 100%;
  height: 100%;
}

svg {
  transform: translateZ(0);
  backface-visibility: hidden;
}

/* Modificar el selector para que solo aplique a las barras del gráfico */
rect:not(.background-rect):hover {
  opacity: 0.8;
}

.x-axis-label {
  font-weight: 400;
  color: #000;
  font-size: 10px;
}

.y-axis-label {
  font-size: 12px;
  font-weight: 400;
  fill: #464555;
}

.axis-title {
  font-weight: 400;
  fill: #464555;
  font-size: 10px;
  font-weight: 500;
  line-height: 12.19px;
  letter-spacing: 0.0025em;
  text-align: center;
}

.x-axis-title {
  font-family: 'Montserrat', sans-serif;
  font-size: 10px;
  font-weight: 500;
  line-height: 12.19px;
  letter-spacing: 0.015em;
  text-align: center;
  fill: #464555;
}
</style>
