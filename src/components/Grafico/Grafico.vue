<template>
  <div>
    <canvas id="grafico"></canvas>
  </div>
</template>

<script>
import Chart from "chart.js/auto";

export default {
  name: "GraficoComportamento",
  props: {},
  methods: {
    renderizarGrafico() {
      const areaGrafico = document.getElementById("grafico");

      if (areaGrafico) {
        let forcaMaxima = 50;

        let valores = [];

        forcaMaxima += 25;
        valores.push(forcaMaxima);

        const corCurva =  forcaMaxima> 0 ? "blue" : "red";
        const config = {
          type: "line",
          data: {
            labels: [0,1,2],
            datasets: [
              {
                label: "Força",
                borderColor: corCurva,
                backgroundColor: corCurva,
                data: valores,
                fill: false,
              },
            ],
          },
          options: this.opcoesGrafico,
        };

        const contexto = areaGrafico.getContext("2d");
        new Chart(contexto, config);
      }
    },
  },
  data: () => {
    return {
      opcoesGrafico: {
        responsive: true,
        title: {
          display: true,
          text: "Dinheiro em caixa",
        },
        tooltips: {
          mode: "index",
          intersect: false,
        },
        hover: {
          mode: "nearest",
          intersect: true,
        },
        scales: {
          xAxes: [
            {
              display: true,
              scaleLabel: {
                display: true,
                labelString: "Dias",
              },
            },
          ],
          yAxes: [
            {
              display: true,
              scaleLabel: {
                display: true,
                labelString: "Renda",
              },
            },
          ],
        },
      },
    };
  },
  mounted() {
    this.renderizarGrafico();
  },
};
</script>

<style>
</style>
