<template>
  <div class="home pt-5 px-5">
    <div class="bar-test-container px-5 py-3 mt-5" s>
      <h2 class="graph-title">Tipo de denuncia</h2>
      <BarTest />
    </div>
  
  </div>
</template>

<script>
import BarChart from '../components/Bar.vue';
import BarTest from '../components/BarTest.vue';  
import Test from '../components/test.vue';

export default {
  components: {
    BarChart,
    BarTest,
    Test
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
.bar-chart-container{
  max-width: 550px;
  max-height: 350px;
  border-radius: 20px;
  box-shadow: 0px 3.15px 8.68px 0px rgba(127, 127, 127, 0.25);
}

.bar-test-container {
  width: 550px;
  height: 350px;
  border-radius: 20px;
  position: relative;
  box-shadow: 0px 3.15px 8.68px 0px #7F7F7F40;
}

</style>
