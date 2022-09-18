<template>
  <div id="app">
    <div class="container d-flex flex-column align-content-end">
      <h1 class="text-center animate__animated animate__fadeInDown titulo">
        <a href="./">TABELA FIPE</a>
      </h1>
      <div
        class="align-self-center d-flex flex-column align-items-center justify-content-center busca text-center mt-2"
      >
        <div class="form-floating animate__animated animate__fadeInDown">
          <select
            class="form-select"
            name="tipoDeVeiculo"
            id="tipoDeVeiculo"
            v-model="tipoDeVeiculo"
          >
            <option disabled value="">Selecione um tipo</option>
            <option value="carros">🚗 Carro</option>
            <option value="motos">🏍️ Moto</option>
            <option value="caminhoes">🚚 Caminhão</option>
          </select>
          <label for="tipoDeVeiculo">Tipo de veículo:</label>
        </div>
        <div
          class="mt-3 form-floating animate__animated"
          v-if="tipoDeVeiculo"
          :class="{ animate__fadeInDown: clickTipo }"
        >
          <select class="form-select" name="marca" id="marca" v-model="marca">
            <option disabled value="">Selecione uma marca</option>
            <option
              v-for="marca in marcas"
              :value="marca.codigo"
              :key="marca.codigo"
            >
              {{ marca.nome }}
            </option>
          </select>
          <label for="marca">Marca:</label>
        </div>
        <div
          class="mt-2 form-floating animate__animated"
          v-if="marca"
          :class="{ animate__fadeInDown: clickMarca }"
        >
          <select
            class="form-select"
            name="modelo"
            id="modelo"
            v-model="modelo"
          >
            <option disabled value="">Selecione um modelo</option>
            <option
              v-for="modelo in modelos"
              :value="modelo.codigo"
              :key="modelo.codigo"
            >
              {{ modelo.nome }}
            </option>
          </select>
          <label for="modelo">Modelo:</label>
        </div>
        <div
          class="mt-2 form-floating animate__animated"
          v-if="modelo"
          :class="{ animate__fadeInDown: clickModelo }"
        >
          <select class="form-select" name="ano" id="ano" v-model="ano">
            <option disabled value="">Selecione um ano</option>
            <option v-for="ano in anos" :value="ano.codigo" :key="ano.codigo">
              {{ ano.nome }}
            </option>
          </select>
          <label for="ano">Ano:</label>
        </div>
        <div class="mt-2 animate__animated animate__fadeInDown" v-if="valor">
          <h5 class="valor">
            <b>Valor: {{ valor }}</b>
          </h5>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TabelaFipe',
  data() {
    return {
      marcas: {},
      modelos: {},
      anos: {},
      tipoDeVeiculo: '',
      marca: '',
      modelo: '',
      ano: '',
      valor: '',
      clickTipo: false,
      clickMarca: false,
      clickModelo: false,
    };
  },
  methods: {
    async fetchMarcas() {
      if (this.tipoDeVeiculo) {
        this.marcas = await (
          await fetch(
            `https://parallelum.com.br/fipe/api/v1/${this.tipoDeVeiculo}/marcas`
          )
        ).json();
      }
    },
    async fetchModelos() {
      if (this.marca) {
        const json = await (
          await fetch(
            `https://parallelum.com.br/fipe/api/v1/${this.tipoDeVeiculo}/marcas/${this.marca}/modelos`
          )
        ).json();
        this.modelos = json.modelos;
      }
    },
    async fetchAnos() {
      if (this.modelo) {
        this.anos = await (
          await fetch(
            `https://parallelum.com.br/fipe/api/v1/${this.tipoDeVeiculo}/marcas/${this.marca}/modelos/${this.modelo}/anos`
          )
        ).json();
      }
      // Tratamento para Veículos Zero KM, pois a API exibe ano "32000" para esses casos
      if (this.anos[0].nome.startsWith('32000'))
        this.anos[0].nome = this.anos[0].nome.replace('32000', 'Zero KM');
    },
    async fetchValor() {
      if (this.ano) {
        this.valor = '';
        const json = await (
          await fetch(
            `https://parallelum.com.br/fipe/api/v1/${this.tipoDeVeiculo}/marcas/${this.marca}/modelos/${this.modelo}/anos/${this.ano}`
          )
        ).json();
        this.valor = json.Valor;
      }
    },
  },
  watch: {
    tipoDeVeiculo() {
      this.marca = this.modelo = this.ano = this.valor = '';
      this.clickTipo = true;
      this.fetchMarcas();
      setTimeout(() => (this.clickTipo = false), '1000');
    },
    marca() {
      this.modelo = this.ano = this.valor = '';
      this.clickMarca = true;
      this.fetchModelos();
      setTimeout(() => (this.clickMarca = false), '1000');
    },
    modelo() {
      this.ano = this.valor = '';
      this.clickModelo = true;
      this.fetchAnos();
      setTimeout(() => (this.clickModelo = false), '1000');
    },
    ano() {
      this.fetchValor();
    },
  },
};
</script>

<style>
body {
  font-family: 'Noto Serif', serif;
  background: linear-gradient(
    to right,
    #e3e4e8,
    #f9f9f9
  ); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
}

a {
  text-decoration: inherit;
  color: inherit;
}

a:hover {
  color: gray;
}

.busca {
  background: white;
  width: 600px;
  height: 400px;
  border-radius: 10px;
  box-shadow: 0px 3px 4px rgba(0, 0, 0, 0.1), 0px 4px 10px rgba(0, 0, 0, 0.2);
}

.titulo,
.valor,
option {
  font-weight: bold;
  color: black;
}

.form-select {
  background-color: transparent;
  color: black;
  width: 400px;
  font-weight: bold;
  border-color: #9ea8ab;
  box-shadow: 0px 3px 4px rgba(0, 0, 0, 0.1), 0px 4px 10px rgba(0, 0, 0, 0.2);
}

option[disabled='disabled'] {
  color: rgba(15, 63, 75, 0.5);
}

/* Responsivo */

@media screen and (max-width: 600px) {
  .container {
    width: 450px;
  }
  .form-select {
    width: 300px;
  }
}

@media screen and (max-width: 450px) {
  .container {
    width: 350px;
  }
  .form-select {
    width: 250px;
  }
}
</style>
