<template>
  <div class="pie-chart-container">
    <svg 
      :width="width" 
      :height="250"
      :viewBox="`-${width/2} -${height/3} ${width} ${height/1.5}`"
      preserveAspectRatio="xMidYMid meet"
    >
      <g>
        <!-- Secciones inactivas -->
        <path
          v-for="(section, index) in computedPieData"
          v-if="activeIndex !== index"
          :key="index"
          :d="getArcPath(section)"
          :fill="section.color || colors[index % colors.length]"
          class="pie-section"
          @mouseover="activeIndex = index"
          @mouseleave="activeIndex = null"
        />
        
        <!-- Líneas y etiquetas -->
        <g 
          v-for="(section, index) in computedPieData" 
          :key="`label-${index}`"
        >
          <polyline
            :points="getLinePoints(section)"
            fill="none"
            :stroke="section.color || colors[index % colors.length]"
            stroke-width="1"
            stroke-linecap="round"
          />
          <circle
            :cx="getEndPoint(section).x"
            :cy="getEndPoint(section).y"
            r="2"
            :fill="section.color || colors[index % colors.length]"
          />
          <text
            :x="getLabelPosition(section).x"
            :y="getLabelPosition(section).y"
            :text-anchor="section.midAngle < Math.PI ? 'start' : 'end'"
            :class="['pie-label', { 'active': activeIndex === index }]"
            dy=".35em"
          >
            {{ chartData[index].label }}
          </text>
        </g>

        <!-- Sección activa -->
        <path
          v-if="activeIndex !== null"
          :d="getArcPath(computedPieData[activeIndex])"
          :fill="computedPieData[activeIndex].color || colors[activeIndex % colors.length]"
          class="pie-section pie-section-active"
          :style="getTransformStyle(computedPieData[activeIndex])"
          @mouseover="activeIndex = activeIndex"
          @mouseleave="activeIndex = null"
        />
        
        <!-- Porcentaje (renderizado al final) -->
        <text
          v-if="activeIndex !== null"
          :transform="getPercentagePosition(computedPieData[activeIndex])"
          class="percentage-text"
          text-anchor="middle"
          dy=".35em"
        >
          {{ getPercentage(computedPieData[activeIndex].value) }}%
        </text>
      </g>
    </svg>
  </div>
</template>

<script>
export default {
  name: 'PieChart',
  props: {
    chartData: {
      type: Array,
      required: true
    },
    width: {
      type: Number,
      default: 450
    },
    height: {
      type: Number,
      default: 400
    },
    colors: {
      type: Array,
      default: () => ['#7CD4F4', '#52B4D2', '#00AAE8', '#040045', '#242432']
    }
  },
  data() {
    return {  
      activeIndex: null
    }
  },
  computed: {
    radius() {
      return Math.min(this.width, this.height) / 2 * 0.55
    },
    total() {
      return this.chartData.reduce((sum, item) => sum + item.value, 0)
    },
    computedPieData() {
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
      const start = this.polarToCartesian(section.startAngle, this.radius)
      const end = this.polarToCartesian(section.endAngle, this.radius)
      const largeArcFlag = section.endAngle - section.startAngle <= Math.PI ? 0 : 1
      
      return `
        M ${start.x} ${start.y}
        A ${this.radius} ${this.radius} 0 ${largeArcFlag} 1 ${end.x} ${end.y}
        L 0 0
        Z
      `
    },
    getLabelPosition(section) {
      const pos = this.polarToCartesian(section.midAngle, this.radius)
      return {
        x: pos.x * 1.1 + (section.midAngle < Math.PI ? 40 : -40),
        y: pos.y
      }
    },
    getLinePoints(section) {
      const pos = this.polarToCartesian(section.midAngle, this.radius)
      const x1 = pos.x * 0.5
      const y1 = pos.y
      const x2 = pos.x * 1.0
      const y2 = y1
      const x3 = pos.x * 1 + (section.midAngle < Math.PI ? 35 : -35)
      return `${x1},${y1} ${x2},${y2} ${x3},${y2}`
    },
    getEndPoint(section) {
      const pos = this.polarToCartesian(section.midAngle, this.radius)
      return {
        x: pos.x * 1 + (section.midAngle < Math.PI ? 35 : -35),
        y: pos.y
      }
    },
    getZoomTransform(index) {
      if (this.activeIndex === index) {
        return 'scale(1.05)';
      }
      return 'scale(1)';
    },
    handleMouseLeave() {
      this.activeIndex = null;
    },
    getTransformStyle(section) {
      const angle = section.midAngle - Math.PI / 2;
      const x = Math.cos(angle) * (this.radius / 2);
      const y = Math.sin(angle) * (this.radius / 2);
      return {
        transformOrigin: `${x}px ${y}px`,
        transform: 'scale(1.1)'
      };
    },
    getPercentage(value) {
      return Math.round((value / this.total) * 100);
    },
    getPercentagePosition(section) {
      const angle = section.midAngle - Math.PI / 2;
      const x = Math.cos(angle) * (this.radius * 0.6);
      const y = Math.sin(angle) * (this.radius * 0.6);
      return `translate(${x}, ${y})`;
    }
  }
}
</script>

<style scoped>
* {
  font-family: 'Montserrat', sans-serif;
}
.pie-chart-container {
  font-family: 'Montserrat', sans-serif;
}

.pie-section {
  transition: all 0.3s ease;
}

.pie-label {
  font-size: 12px;
  fill: #464555;
  font-weight: 400;
}

.pie-label.active {
  font-weight: 500;
}
.percentage-text {
  font-size: 20px;
  fill: white;
  pointer-events: none;
  font-weight: 400;
}

</style> 