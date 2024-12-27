<template>
  <div class="home pt-5 px-5">
    <div class="bar-test-container px-5 py-3 mt-5" s>
      <h2 class="graph-title">Tipo de denuncia</h2>
      <BarTest />
    </div>
  
    <div class="bar-test-container px-5 py-3 mt-5" s>
      <h2 class="graph-title">Total de denuncias por persona asignada</h2>
      <Pie 
        :chartData="myData"
      />
    </div>
  
  </div>
</template>

<script>
import BarChart from '../components/Bar.vue';
import BarTest from '../components/BarTest.vue';
import Pie from '../components/Pie.vue'

export default {
  components: {
    BarChart,
    BarTest,
    Pie
  },
  data() {
    return {
      myData: [
        { value: 20, label: 'GESTOR 01', color: '#00AAE8' },
        { value: 30, label: 'GESTOR 02', color: '#040045' },
        { value: 20, label: 'ADMINIST 01', color: '#242432' },
        { value: 10, label: 'OTROS', color: '#7CD4F4' },
        { value: 20, label: 'NO ASIGNADAS', color: '#52B4D2' }
      ]
    }
  }
};
</script>

<style>
.graph-title {
  font-family: Montserrat;
  font-size: 14px;
  font-weight: 500;
  line-height: 17.07px;
  letter-spacing: 0.0025em;
  text-align: left;
  text-underline-position: from-font;
  text-decoration-skip-ink: none;

}

.bar-test-container {
  width: fit-content;
  height: fit-content;
  border-radius: 20px;
  position: relative;
  box-shadow: 0px 3.15px 8.68px 0px #7F7F7F40;
}

</style>
