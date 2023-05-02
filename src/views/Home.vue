<template>
  <div class="container">
    <div class="row mt-2">
      <!-- Início lado esquerdo -->
      <div class="col mb-2">
        <div class="card palco">
          <div class="card-header"></div>

          <div class="card-body bg-pokebola bg-normal">
            <div class="pokemon">
              <!-- <transition name="slide"> -->
              <!-- <transition name="zoom"> -->
              <!-- <transition name="flip"> -->
              <!-- <transition name="animacao1"> -->
              <!-- <transition name="pulo" type="animation"> -->
              <!-- <transition name="pulo" :duration="{ enter: 2000, leave: 1000 }"> -->
              <!-- <transition
                enter-from-class="entrada-estado-inicial"
                enter-active-class="transicao"
                enter-to-class="entrada-estado-final"
                leave-from-class="saida-estado-inicial"
                leave-active-class="transicao"
                leave-to-class="saida-estado-final"
              > -->
              <!-- <transition
                @before-enter="antesDaEntrada"
                @enter="duranteAEntrada"
                @after-enter="aposAEntrada"
                @enter-cancelled="quandoEntradaCancelada"
                @before-leave="antesDaSaida"
                @leave="duranteASaida"
                @after-leave="aposASaida"
                @leave-cancelled="quandoSaidaCancelada"
                :duration="{ enter: 2000, leave: 1000 }"
                enter-active-class="animate__animated animate__bounceIn"
                leave-active-class="animate__animated animate__bounceOut"
              > -->

              <transition
                @after-enter="exibirEvolucoesTransicao"
                @before-leave="ocultarEvolucoesTransicao"
                enter-active-class="animate__animated animate__bounceIn"
                leave-active-class="animate__animated animate__bounceOut"
              >
                <img
                  :src="require(`@/assets/imgs/pokemons/${pokemon.imagem}`)"
                  v-if="exibir"
                />
              </transition>

              <div class="evolucoes">
                <transition name="fade" v-for="e in pokemon.evolucoes" :key="e">
                  <img
                    :src="
                      require('@/assets/imgs/pokemons/' +
                        e.toString().padStart(3, '0') +
                        '.png')
                    "
                    v-if="exibirEvolucoes"
                  />
                </transition>
              </div>
            </div>
          </div>

          <div class="card-footer">
            <nav class="nav nav-pills nav-fill">
              <!-- Menu de navegação -->
              <router-link
                class="nav-item nav-link text-white"
                :to="{ path: '/sobre' }"
                exact-active-class="active"
                >Sobre</router-link
              >
              <router-link
                class="nav-item nav-link text-white"
                :to="{ path: '/status' }"
                exact-active-class="active"
                >Status</router-link
              >
              <router-link
                class="nav-item nav-link text-white"
                :to="{ path: '/habilidades' }"
                exact-active-class="active"
                >Habilidades</router-link
              >
            </nav>

            <div class="detalhes">
              <!-- Exibe dados de acordo com o menu de navegação -->
              <router-view
                v-slot="{ Component }"
                :pokemon="pokemon"
                @adicionarHabilidade="adicionarHabilidade"
                @removerHabilidade="removerHabilidade"
              >
                <transition
                  enter-active-class="animate__animated animate__zoomInDown"
                >
                  <component :is="Component" />
                </transition>
              </router-view>
            </div>
          </div>
        </div>
      </div>
      <!-- Fim lado esquerdo -->

      <!-- Início lado direito -->
      <div class="col mb-2 pokedex">
        <div class="row">
          <div class="col">
            <h1>Pokédex</h1>
          </div>
        </div>

        <div class="row">
          <div class="col">
            <select class="form-select" v-model="ordenacao">
              <option value="" disabled>Ordenar Pokémon</option>
              <option value="1">Id crescente</option>
              <option value="2">Id decrescrente</option>
              <option value="3">De A - Z</option>
            </select>
          </div>

          <div class="col">
            <input
              type="text"
              class="form-control"
              placeholder="Pesquisar pokémon"
            />
          </div>
        </div>

        <div class="row">
          <div class="pokedex-catalogo">
            <!-- Início listagem dinâmica -->
            <div
              v-for="p in pokemons"
              :key="p.id"
              :class="`cartao-pokemon bg-${p.tipo}`"
              @click="analisarPokemon(p)"
            >
              <h1>{{ p.id }}{{ " " }}{{ p.nome }}</h1>
              <span>{{ p.tipo }}</span>
              <div class="cartao-pokemon-img">
                <transition
                  appear
                  enter-active-class="animate__animated animate__fadeInDown"
                >
                  <img :src="require(`@/assets/imgs/pokemons/${p.imagem}`)" />
                </transition>
              </div>
            </div>
            <!-- Fim listagem dinâmica -->
          </div>
        </div>
      </div>
      <!-- Fim lado direito -->
    </div>
  </div>
</template>

<script>
import "@/assets/css/animacoes.css";

export default {
  name: "Home",
  data() {
    return {
      exibir: false,
      exibirEvolucoes: false,
      pokemon: {},
      pokemons: [],
      ordenacao: "",
    };
  },
  watch: {
    ordenacao(valorNovo) {
      //método sort
      //return 1 para indicar que a ordem está correta
      //return -1 para indicar que a ordem está incorreta (trocados)
      //return 0 para indicar que são iguais (nada deve ser feito)

      //ordenação por id crescente
      if (valorNovo == 1) {
        this.pokemons.sort((proximo, atual) => {
          if (atual.id < proximo.id) {
            return 1;
          } else if (atual.id > proximo.id) {
            return -1;
          }

          return 0;
        });
      }

      //ordenação por id decrescente
      if (valorNovo == 2) {
        this.pokemons.sort((proximo, atual) => {
          if (atual.id < proximo.id) {
            return -1;
          } else if (atual.id > proximo.id) {
            return 1;
          }

          return 0;
        });
      }

      //ordenação alfabética A - Z
      if (valorNovo == 3) {
        this.pokemons.sort((proximo, atual) => {
          // 1 caso a ordem esteja correta
          if (atual.nome < proximo.nome) {
            return 1;
          }
          // -1 caso a ordem esteja errada (necessário inverter posições)
          if (atual.nome > proximo.nome) {
            return -1;
          }
          // 0 caso nenhuma ação seja necessária
          return 0;
        });
      }
    },
  },
  created() {
    fetch("http://localhost:3000/pokemons")
      .then((resposta) => {
        return resposta.json();
      })
      .then((data) => {
        // console.log(data);
        this.pokemons = data;
      });
  },

  methods: {
    analisarPokemon(p) {
      let mudaPokemonAnalisado = false;

      //analisar se o pokemon atual é diferente do pokemon clicado
      //se o atributo exibir é true
      if (this.pokemon.id != p.id && this.exibir == true) {
        setTimeout(() => {
          this.analisarPokemon(p);
        }, 1000);

        mudaPokemonAnalisado = true;
      }

      this.pokemon = p;
      this.exibir = !this.exibir;
      this.exibirEvolucoes = !this.exibirEvolucoes;

      // se a ação for de ocultar o Pokémon
      // se a ação recursica não for chamada
      if (!this.exibir && !mudaPokemonAnalisado) {
        this.pokemon = {};
      }
    },

    exibirEvolucoesTransicao() {
      this.exibirEvolucoes = true;
    },

    ocultarEvolucoesTransicao() {
      this.exibirEvolucoes = false;
    },

    antesDaEntrada(el) {
      console.log("Antes da entrada", el);
    },

    // duranteAEntrada(el, done) {
    duranteAEntrada(el) {
      console.log("Durante a entrada", el);

      //done(); //indica a conclusão da transição (entrada)
    },

    aposAEntrada(el) {
      console.log("Após a entrada", el);
    },

    quandoEntradaCancelada(el) {
      console.log("Quando a entrada é cancelada", el);
    },

    antesDaSaida(el) {
      console.log("Antes da saída", el);
    },

    // duranteASaida(el, done) {
    duranteASaida(el) {
      console.log("Durante a saída", el);

      //done(); //indica a conclusão da transição (saída)
    },

    aposASaida(el) {
      console.log("Após a saída", el);
    },

    quandoSaidaCancelada(el) {
      console.log("Quando a saída é cancelada", el);
    },

    adicionarHabilidade(habilidade) {
      if (this.pokemon.habilidades) {
        this.pokemon.habilidades.push(habilidade);
      }
    },
    removerHabilidade(indice) {
      if (this.pokemon.habilidades[indice]) {
        this.pokemon.habilidades.splice(indice, 1);
      }
    },
  },
};
</script>

<style>
body {
  background-color: #dee3eb;
}
</style>

<style scoped>
.pokedex {
  padding: 20px;
  background-color: #ffffff;
  -webkit-box-shadow: 2px 2px 10px rgba(200, 200, 200, 0.77);
  -moz-box-shadow: 2px 2px 10px rgba(200, 200, 200, 0.77);
  box-shadow: 2px 2px 10px rgba(200, 200, 200, 0.77);
  border-radius: 10px;
}

.pokedex-catalogo {
  overflow: auto;
  display: flex;
  flex-wrap: wrap;
  height: 400px;
  width: 98%;
  margin-top: 10px;
}

.cartao-pokemon {
  position: relative;
  margin: 5px;
  width: 150px;
  height: 115px;
  cursor: pointer;
  border-radius: 5px;
  -webkit-box-shadow: 2px 2px 2px rgba(200, 200, 200, 0.77);
  -moz-box-shadow: 2px 2px 2px rgba(200, 200, 200, 0.77);
  box-shadow: 2px 2px 2px rgba(200, 200, 200, 0.77);
}

.cartao-pokemon h1 {
  color: #fff;
  font-size: 14px;
  margin: 5px 0px 0px 5px;
  padding: 0px;
}

.cartao-pokemon span {
  color: #fff;
  position: absolute;
  background: rgba(255, 255, 255, 0.3);
  font-size: 12px;
  margin: 10px 0px 0px 5px;
  padding: 5px 10px 5px 10px;
  border-radius: 25px;
}

.cartao-pokemon img {
  max-width: 60%;
  max-height: 60%;
  float: right;
}

.bg-grama {
  background-color: #2d8f78;
}

.bg-fogo {
  background-color: #e47373;
}

.bg-agua {
  background-color: #5a9ed2;
}

.bg-inseto {
  background-color: #26d3ab;
}

.bg-normal {
  background-color: #cecece;
}

.bg-pokebola {
  background-image: url("~@/assets/imgs/pokebola.png");
  background-repeat: no-repeat;
  background-position: bottom right;
}

.palco {
  color: #fff;
  background-color: #333;
  -webkit-box-shadow: 2px 2px 10px rgba(230, 223, 223, 0.77);
  -moz-box-shadow: 2px 2px 10px rgba(230, 223, 223, 0.77);
  box-shadow: 2px 2px 10px rgba(230, 223, 223, 0.77);
  border-radius: 10px;
}

.pokemon {
  display: block;
  text-align: center;
  height: 215px;
}

.detalhes {
  margin: 20px 30px 20px 30px;
}

.evolucoes {
  position: absolute;
  top: 0px;
  right: 0px;
  height: 70px;
}

.evolucoes img {
  cursor: pointer;
  max-width: 100%;
  max-height: 100%;
}
</style>
