<template>
  <div class="home pt-5 px-5">
    <div class="bar-test-container px-5 py-3 mt-5" s>
      <h2 class="graph-title">Tipo de denuncia</h2>
      <Bar />
    </div>
  
    <div class="bar-test-container px-5 py-3 mt-5" s>
      <h2 class="graph-title">Total de denuncias por persona asignada</h2>
      <Pie 
        :chartData="myPieData"
      />
    </div>
  
    <div class="bar-test-container px-5 py-3 mt-5" s>
      <h2 class="graph-title">Estado de las denuncias</h2>
      <BarHorizontal 
        :chartData="[
          { value: 100, label: 'Todas' },
          { value: 85, label: 'Nuevas' },
          { value: 75, label: 'En curso' },
          { value: 65, label: 'Descartadas' },
          { value: 55, label: 'Finalizadas' }
        ]"
        :width="400"
        :height="300"
      />
    </div>
  

  </div>
</template>

<script>
import Bar from '../components/Bar.vue';
import Pie from '../components/Pie.vue'
import BarHorizontal from '../components/BarHorizontal.vue'

export default {
  components: {
    Bar,
    Pie,
    BarHorizontal
  },
  data() {
    return {
      myPieData: [
        { value: 45, label: 'GESTOR 01', color: '#00AAE8' },
        { value: 30, label: 'GESTOR 02', color: '#040045' },
        { value: 25, label: 'NO ASIGNADAS', color: '#52B4D2' }  
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
