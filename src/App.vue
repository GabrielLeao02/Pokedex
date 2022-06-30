<template>
  <v-app>
    <v-container>
      <v-row>
        <v-container>
          <h1 class="mb-8 text-center" style="font-size: 5rem; color: whitesmoke;
          background: linear-gradient(90deg, rgba(2,0,36,1) 0%, rgba(105,9,121,1) 19%, rgba(0,212,255,1) 100%);">Busca Pokemons</h1>
        </v-container>
      </v-row>
    </v-container>
    <v-container>
      <v-container>
        <v-text-field v-model="search" label="Pesquisar" placeholder="Pikachu" solo>
        </v-text-field>
        <v-row>
          <v-col cols="3" v-for="pokemon in filteredItems" :key="pokemon.name">
            <v-card @click="buscaId(get_id(pokemon))">
              <v-container>
                <v-row class="mx-0 d-flex justify-center">
                  <img width="60%"
                    :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${get_id(pokemon)}.png`"
                    :alt="pokemon.name">
                </v-row>
                <h2 class="text-center">{{ get_name(pokemon) }}</h2>
              </v-container>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-container>
    <ModalPokemons 
      v-if="dialog"
      @fechaModal = 'dialog = false'
      :ids = "num"
      :condicao = "conditiona"/>
  </v-app>
</template>

<script>
import axios from 'axios';
import ModalPokemons from './components/ModalEditar/Modal-Pokemons.vue';

export default {
  name: 'App',
  components: { ModalPokemons },
  
  data: () => ({
    pokemons: [],
    search: "",
    dialog: false,
    testea(){
      this.dialog = !this.dialog;
      console.log('ok');
    }, 
    num: null,
    conditiona: true,
  }),
  mounted() {
    //conexao api
    axios.get("https://pokeapi.co/api/v2/pokemon?limit=100000&offset=0").then((response) => {
      this.pokemons = response.data.results;
      //console.log(response.data);
    })
  },
  methods: {
    
    get_id(pokemon) {
      return Number(pokemon.url.split("/")[6]);
    },
    
    get_name(pokemon) {
      return pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1);
    },
    //captura id do pokemon
    buscaId(id) {
      console.log(id);
      this.dialog = !this.dialog;
      this.num = Number(id);
    }
  },
  
  computed: {
  //filtro pelo textFild
  filteredItems() {
      return this.pokemons.filter((item) => {
        return item.name.includes(this.search.toLowerCase())
      });
    },
  }
}
</script>

<style>
#app {
  background: linear-gradient(to bottom right,
      rgba(10, 10, 10, 1),
      rgba(12, 39, 63, 1)) no-repeat center center fixed !important;
  -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
  background-size: cover !important;
  background-position: center;
  min-height: 100vh;
}
</style>