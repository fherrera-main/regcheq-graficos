<template>
  <div class="doughnut-chart-container">
    <svg 
      :width="width" 
      :height="height"
      :viewBox="`-${width/2} -${height/2} ${width} ${height}`"
      preserveAspectRatio="xMidYMid meet"
    >
      <g>
        <!-- Todas las secciones en un solo grupo -->
        <path
          v-for="(section, index) in sortedSections"
          :key="section.originalIndex"
          :d="getArcPath(section)"
          :fill="section.color || colors[section.originalIndex % colors.length]"
          class="doughnut-section"
          @mouseenter="activeIndex = section.originalIndex"
          @mouseleave="activeIndex = null"
          :class="{ 'is-active': activeIndex === section.originalIndex }"
        />

        <!-- Etiquetas -->
        <text
          v-for="(section, index) in computedDoughnutData"
          :key="`label-${index}`"
          :x="getLabelPosition(section).x"
          :y="getLabelPosition(section).y"
          :text-anchor="section.midAngle < Math.PI ? 'start' : 'end'"
          class="doughnut-label"
        >
          {{ chartData[index].label }}
        </text>

        <!-- Añadir el texto del porcentaje en el centro -->
        <text
          v-if="activeIndex !== null"
          x="0"
          y="0"
          text-anchor="middle"
          alignment-baseline="middle"
          class="percentage-label"
        >
          {{ getPercentage(chartData[activeIndex].value) }}%
        </text>
      </g>
    </svg>
  </div>
</template>

<script>
export default {
  name: 'Doughnut',
  props: {
    chartData: {
      type: Array,
      default: () => [
        { value: 30, label: 'Cliente' },
        { value: 25, label: 'Proveedor' },
        { value: 25, label: 'Colaborador' },
        { value: 20, label: 'Otro' }
      ]
    },
    width: {
      type: Number,
      default: 400
    },
    height: {
      type: Number,
      default: 400
    },
    colors: {
      type: Array,
      default: () => ['#040045', '#52B4D2', '#00AAE8', '#7CD4F4']
    }
  },
  data() {
    return {
      activeIndex: null
    }
  },
  computed: {
    radius() {
      return Math.min(this.width, this.height) / 2 * 0.8
    },
    innerRadius() {
      return this.radius * 0.65
    },
    total() {
      return this.chartData.reduce((sum, item) => sum + item.value, 0)
    },
    computedDoughnutData() {
      let currentAngle = 0
      return this.chartData.map(item => {
        const percentage = item.value / this.total
        const startAngle = currentAngle
        const endAngle = currentAngle + (percentage * Math.PI * 2)
        const midAngle = startAngle + (endAngle - startAngle) / 2
        
        currentAngle = endAngle
        
        return {
          ...item,
          startAngle,
          endAngle,
          midAngle
        }
      })
    },
    sortedSections() {
      return this.computedDoughnutData.map((section, index) => ({
        ...section,
        originalIndex: index
      })).sort((a, b) => {
        if (this.activeIndex === a.originalIndex) return 1;
        if (this.activeIndex === b.originalIndex) return -1;
        return 0;
      });
    }
  },
  methods: {
    polarToCartesian(angle, radius) {
      return {
        x: Math.cos(angle - Math.PI / 2) * radius,
        y: Math.sin(angle - Math.PI / 2) * radius
      }
    },
    getArcPath(section) {
      const startOuter = this.polarToCartesian(section.startAngle, this.radius)
      const endOuter = this.polarToCartesian(section.endAngle, this.radius)
      const startInner = this.polarToCartesian(section.startAngle, this.innerRadius)
      const endInner = this.polarToCartesian(section.endAngle, this.innerRadius)
      
      const largeArcFlag = section.endAngle - section.startAngle <= Math.PI ? 0 : 1
      
      return `
        M ${startOuter.x} ${startOuter.y}
        A ${this.radius} ${this.radius} 0 ${largeArcFlag} 1 ${endOuter.x} ${endOuter.y}
        L ${endInner.x} ${endInner.y}
        A ${this.innerRadius} ${this.innerRadius} 0 ${largeArcFlag} 0 ${startInner.x} ${startInner.y}
        Z
      `
    },
    getLabelPosition(section) {
      const labelRadius = this.radius + 20
      const pos = this.polarToCartesian(section.midAngle, labelRadius)
      return {
        x: pos.x,
        y: pos.y
      }
    },
    getPercentage(value) {
      return Math.round((value / this.total) * 100)
    }
  }
}
</script>

<style scoped>
* {
  font-family: 'Montserrat', sans-serif;
}
.doughnut-chart-container {
  font-family: 'Montserrat', sans-serif;
}

.doughnut-section {
  transition: all 0.3s ease;
  transform-origin: center;
  transform-box: fill-box;
}

.doughnut-section:hover,
.doughnut-section.is-active {
  transform: scale(1.05);
  z-index: 1;
  filter: drop-shadow(0 0 3px rgba(0,0,0,0.2));
}

.doughnut-label {
  fill: #242432;
  font-size: 12px;
  font-weight: 500;
  line-height: 14.63px;
  letter-spacing: 0.004em;
}

.percentage-label {
  fill: #464555;
  font-family: Montserrat;
  font-size: 30.15px;
  font-weight: 500;
  line-height: 36.75px;
  opacity: 0;
  animation: fadeIn 0.3s ease forwards;
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
</style>

