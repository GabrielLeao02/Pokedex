<template >

  <v-dialog v-model="dialog" width="800" v-if="condicao">

    <div v-if="this.condicaoBuscaSkill">
      {{ buscaSkills(ids) }}
    </div>

    <v-card>

      <v-container class="px-6">
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="red darken-1" text @click="$emit('fechaModal')">
            Fechar
          </v-btn>
        </v-card-actions>

        <v-row class="d-flex align-center">

          <v-col cols="4">
            <img
              width="70%"
              :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${ids}.png`"
              alt="filteredItems.name[]"
            />
          </v-col>

          <v-col cols="8">

            <h1>{{ get_name(filteredItems[ids - 1]) }}</h1>

            <v-chip
              label
              v-for="type in pokemons_selected.types"
              :key="type.slot"
              class="mr-2"
              >{{ type.type.name }}
            </v-chip>

            <v-divider class="my-4"></v-divider>

            <v-chip label class="mr-2"
              >Altura: {{ pokemons_selected.height * 2.54 }} cm
            </v-chip>

            <v-chip label
              >Peso:
              {{ (pokemons_selected.weight * 0.453592).toFixed(0) }} kg</v-chip
            >

          </v-col>

        </v-row>

        <h2>Estatísticas</h2>

        <Grafico />

        <h2>Movimentos</h2>
        
        <v-simple-table>

          <template>

            <thead>

              <tr>

                <th class="text-left">Level</th>

                <th class="text-rigth">Name</th>

              </tr>

            </thead>

            <tbody>
              
              <tr
                v-for="item in filter_moves(pokemons_selected)"
                :key="item.move.name"
              >

                <td>{{ get_level(item) }}</td>

                <td>{{ item.move.name }}</td>

              </tr>

            </tbody>

          </template>

        </v-simple-table>

      </v-container>

    </v-card>

  </v-dialog>

</template>

<Grafico :levelpoke = "level" />

<script>

import axios from "axios";
import Chart from 'chart.js/auto'
import Grafico from '../Grafico/Grafico.vue'

export default {  
  components: { Grafico },
  props: {
    ids: Number,
    condicao: Boolean,
  },
  data() {
    return {
      pokemons: [],
      level: [],
      namelevel:[],
      search: "",
      dialog: true,
      id: null,
      pokemons_selected: [],
      condicaoBuscaSkill: true,
     
    };
  },
  mounted() {
    axios
      .get("https://pokeapi.co/api/v2/pokemon?limit=100000&offset=0")
      .then((response) => {
        this.pokemons = response.data.results;
      });
  },
  methods: {
    get_name(pokemon) {
      return pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1);
    },
    buscaSkills(ids) {
      axios.get(`https://pokeapi.co/api/v2/pokemon/${ids}`).then((response) => {
        this.pokemons_selected = response.data;
        this.condicaoBuscaSkill = false;
        console.log(response.data);
      });
    },
    get_level(move) {
      for (let version of move.version_group_details) {
        if (
          version.version_group.name == "sword-shield" &&
          version.move_learn_method.name == "level-up"
        ) {
          this.namelevel.push(move.move.name);
           this.level.push(version.level_learned_at);
                  return version.level_learned_at;
        }
      }
      return 0;
    },
    filter_moves(pokemon) {
      return pokemon.moves.filter((item) => {
        let include = false;
        for (let version of item.version_group_details) {
          if (
            version.version_group.name == "sword-shield" &&
            version.move_learn_method.name == "level-up"
          ) {
            include = true;
          }
        }
        return include;
      });
    },
    renderizarGrafico() {
      const areaGrafico = document.getElementById("grafico");

      if (areaGrafico) {
        let valorEmCaixa = 500;        
        let valores = [];
        valorEmCaixa += 25;
        valores.push(valorEmCaixa);
        const corCurva = valorEmCaixa > 0 ? "blue" : "red";
        const config = {
          type: "line",
          data: {
            labels: "25-06-2020",
            datasets: [
              {
                label: "Comportamento do seu dinheiro",
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
  //busca os pekemos
  computed: {
    filteredItems() {
      return this.pokemons.filter((item) => {
        return item.name.includes(this.search.toLowerCase());
      });
    },
  },
};
</script>
