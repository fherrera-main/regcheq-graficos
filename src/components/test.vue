<template>
  <div>
    <svg :width="514" :height="400" viewBox="0 0 514 400" fill="none" xmlns="http://www.w3.org/2000/svg">
      <!-- Líneas de cuadrícula -->
      <template v-if="showGrid">
        <line 
          v-for="tick in yTicks" 
          :key="'grid-'+tick"
          x1="28" 
          x2="373" 
          :y1="getYAxisPosition(tick)" 
          :y2="getYAxisPosition(tick)" 
          stroke="#E5E3F6" 
          stroke-width="0.5"
          opacity="0.8"
        />
      </template>

      <!-- Barras -->
      <g v-for="(item, index) in normalizedData" :key="index">
        <rect
          :x="getBarX(index)"
          :y="getBarY(item)"
          :width="33.125"
          :height="getBarHeight(item)"
          :fill="getBarColor(index)"
          rx="6.30952"
          v-b-tooltip="{
            variant: 'dark',
            placement: 'top',
            delay: { show: 100, hide: 100 }
          }"
          :title="tooltipFormat(data[index], labels[index])"
        />
      </g>

      <!-- Labels eje Y -->
      <g v-for="(tick, index) in yTicks" :key="'label-'+index">
        <text
          v-if="tick !== 0"
          x="25"
          :y="getYAxisPosition(tick)"
          text-anchor="end"
          font-size="12"
          fill="#464555"
        >
          {{ formatYAxis(tick) }}
        </text>
      </g>

      <!-- Línea base -->
      <line x1="24" y1="365.803" x2="393" y2="365.803" stroke="#464555" stroke-width="0.394345"/>
    </svg>
  </div>
</template>

<script>
export default {
  name: 'Test',
  props: {
    data: {
      type: Array,
      default: () => [46, 60, 12, 46]
    },
    labels: {
      type: Array,
      default: () => ['Acoso laboral', 'Acoso sexual', 'Violencia en el trabajo', 'Abuso de poder']
    },
    showGrid: {
      type: Boolean,
      default: true
    },
    tooltipFormat: {
      type: Function,
      default: (value, label) => `${label}: ${value}`
    },
    formatYAxis: {
      type: Function,
      default: value => value
    }
  },
  computed: {
    normalizedData() {
      const maxTick = Math.max(...this.yTicks);
      return this.data.map(value => (value / maxTick) * 100);
    },
    maxValueIndex() {
      return this.data.indexOf(Math.max(...this.data));
    },
    yTicks() {
      const maxValue = Math.max(...this.data);
      const ticks = [0, 10];
      let currentValue = 50;
      while (currentValue <= maxValue) {
        ticks.push(currentValue);
        currentValue += 50;
      }
      if (ticks[ticks.length - 1] < maxValue) {
        ticks.push(currentValue);
      }
      return ticks;
    }
  },
  methods: {
    getBarX(index) {
      // Ajustado para las posiciones específicas de tu SVG
      return 79.28 + (index * 79);
    },
    getBarY(value) {
      // Ajustado para el nuevo viewBox
      return 365.803 - this.getBarHeight(value);
    },
    getBarHeight(value) {
      // Aumenta la altura máxima de las barras
      return (value * 258.3333) / 100; // Aumentado de 88.3333 a 258.3333
    },
    getYAxisPosition(tick) {
      // Ajustado para la nueva escala
      return 365.803 - ((tick / Math.max(...this.yTicks)) * 258.3333);
    },
    getBarColor(index) {
      return index === this.maxValueIndex ? '#040045' : '#82D7EB';
    }
  }
}
</script>

<style scoped>
* {
  font-family: 'Montserrat', sans-serif;
}

rect {
  transition: all 0.3s ease;
}

rect:hover {
  opacity: 0.8;
}
</style>
