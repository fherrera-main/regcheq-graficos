<template>
  <div class="home pt-5 px-5 b-row">
    <b-row no-gutters>
      <b-col cols="12" md="6" class="p-3">
        <div class=" px-5 py-3">
          <h2 class="graph-title">Tipo de denuncia</h2>
          <Bar />
        </div>
      </b-col>
      <b-col cols="12" md="6" class="p-3">
        <div class=" px-5 py-3">
          <h2 class="graph-title">Total de denuncias por persona asignada</h2>
          <Pie :chartData="myPieData" />
        </div>
      </b-col>
      <b-col cols="12" md="6" class="p-3">
        <div class=" px-5 py-3">
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
      </b-col>
      <b-col cols="12" md="6" class="p-3">
        <div class=" px-5 py-3">
          <h2 class="graph-title">Total denunciante</h2>
          <BarVariante :chartData="[
            { value: 45, label: 'Anónimos' },
            { value: 70, label: 'No anónimos' }
          ]" />
        </div>
      </b-col>
      <b-col cols="12" md="6" class="p-3">
        <div class=" px-5 py-3">
          <h2 class="graph-title">Plazos</h2>
          <BarVariant2 :chartData="myBarVariant2Data" />
        </div>
      </b-col>
    </b-row>
  </div>
</template>

<script>
import Bar from '../components/Bar.vue';
import Pie from '../components/Pie.vue'
import BarHorizontal from '../components/BarHorizontal.vue'
import BarVariante from '../components/BarVariante.vue'
import BarVariant2 from '../components/BarVariant2.vue' 
export default {
  components: {
    Bar,
    Pie,
    BarHorizontal,
    BarVariante,
    BarVariant2
  },
  data() {
    return {
      myPieData: [
        { value: 35, label: 'GESTOR 01', color: '#00AAE8' },
        { value: 25, label: 'GESTOR 02', color: '#040045' },
        { value: 15, label: 'ADMINIST 01', color: '#242432' },
        { value: 5, label: 'OTROS', color: '#7CD4F4' },
        { value: 20, label: 'NO ASIGNADAS', color: '#52B4D2' }
      ],
      myBarVariant2Data: [
        { rango: 'Menos de 1 día', nuevas: 48, enCurso: 35 },
        { rango: 'Entre 1 y 3 días', nuevas: 57, enCurso: 45 },
        { rango: 'Entre 3 y 7 días', nuevas: 48, enCurso: 35 },
        { rango: 'Más de una semana', nuevas: 30, enCurso: 19 }
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
  width: 100%;
  height: 100%;
  height: 350px;
  border-radius: 20px;
  position: relative;
  box-shadow: 0px 3.15px 8.68px 0px #7F7F7F40;
}

</style>
