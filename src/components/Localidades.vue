<template>
  <div class="igbe-localidades">
    <h1>Estados</h1>
    <div class="list-group">
      <div class="list-group-item" v-for="estado in estados" :key="estado.id">
        <div class="row">
          <div class="col-10">{{ estado.name || estado.nome }}</div>
          <div class="col-2">
            <button class="btn btn-light btn-block" @click="buscarMunicipios(estado)" type="button">Municípios</button>
          </div>
        </div>
        <div v-if="estado.exibirMunicipios">
          <h2>Municípios do {{ estado.name || estado.nome }}</h2>
          <div class="list-group my-3">
            <div class="list-group-item" v-for="(municipio, municipioIndex) in municipios[estado.id]" :key="municipio.id">
              <div class="row">
                <div class="col-10">{{ municipio.name || municipio.nome }}</div>
                <div class="col-2">
                  <button
                    class="btn btn-light btn-block"
                    @click="alternarMicrorregiao(estado, municipioIndex)"
                    type="button">Microrregião</button>
                </div>
              </div>
              <div :hidden="!municipio.exibirDetalhes">
                <h3>Detalhes de {{ municipio.name || municipio.nome }}</h3>
                <div class="list-group my-3">
                  <div class="list-group-item">
                    <div class="row">
                      <div class="col-10">Microrregião {{ municipio.microrregiao.name || municipio.microrregiao.nome }}</div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import Vue from 'vue';

export default {
  name: 'Localidades',
  data () {
    return {
      estados: [],
      estadoSelecionado: null,
      municipios: {},
      baseUrl: 'http://servicodados.ibge.gov.br/api/v1/localidades'
    }
  },
  async mounted () {
    const result = await this.getData(`${this.baseUrl}/estados`);

    this.estados = this.sortByName(result);
  },
  methods: {
    alternarMunicipios (estado) {
      Vue.set(estado, 'exibirMunicipios', !estado.exibirMunicipios);
    },
    alternarMicrorregiao ({ id }, municipioIndex) {
      const municipio = this.municipios[id][municipioIndex];

      Vue.set(this.municipios[id][municipioIndex], 'exibirDetalhes', !municipio.exibirDetalhes);
    },
    getData (url) {
      return axios.get(url).then(({ data }) => data);
    },
    async buscarMunicipios (estado) {
      if (!estado.municipios || !estado.municipios.length) {
        this.municipios[estado.id] = await this.getData(`${this.baseUrl}/estados/${estado.id}/municipios`);
      }

      this.alternarMunicipios(estado);
    },
    sortByName (array) {
      return array.sort((a, b) => {
        if (a.nome > b.nome) {
          return 1;
        }

        if (a.nome < b.nome) {
          return -1;
        }

        return 0;
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">

</style>