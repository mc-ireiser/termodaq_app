<template>
  <div>
    <nav aria-label="breadcrumb">
      <ol class="breadcrumb">
        <li class="breadcrumb-item active" aria-current="page">
          Muestreo: {{ficha.titulo}} - {{ficha.lugar}} -
          <a href="/tablero/estudios/listado">volver al listado</a>
        </li>
      </ol>
    </nav>
    <div class="app flex-row">
      <div class="container">
        <div class="row">
          <div class="col-sm-12">

            <div class="card mx-4 pl-2">
              <h5 class="my-2">Titulo: {{ficha.titulo}}</h5>
              <h6>Lugar: {{ficha.lugar}}</h6>              
            </div>

            <div class="card mx-4 pl-2">
              <h6 class="my-2">descripcion</h6>
              <p>{{ficha.descripcion}}</p>
            </div>

            <div class="card mx-4 pl-2">
              <h6 class="my-2">Investigadores</h6>
              <p></p>
            </div>

            <div class="card mx-4 pl-2">
              <Plotly :data="agua" :layout="layoutAgua" :display-mode-bar="false"></Plotly>
            </div>

            <div class="card mx-4 pl-2">
              <Plotly :data="aire" :layout="layoutAire" :display-mode-bar="false"></Plotly>
            </div>

            <div class="card mx-4 pl-2">
              <Plotly :data="uv" :layout="layoutUv" :display-mode-bar="false"></Plotly>
            </div>

            <div class="card mx-4 pl-2">
              <Plotly :data="presion" :layout="layoutPresion" :display-mode-bar="false"></Plotly>
            </div>

            <div class="card mx-4 pl-2">
              <Plotly :data="grafica_combinada" :layout="layoutCombinado" :display-mode-bar="false"></Plotly>
            </div>

            <div class="card mx-4">
              <table class="table table-hover">
                <thead>
                  <tr>
                    <th scope="col">Fecha</th>
                    <th scope="col">Hora</th>
                    <th scope="col">Latitud</th>
                    <th scope="col">Longitud</th>
                    <th scope="col">Temp. Equipo</th>
                    <th scope="col">Temp. Aire</th>
                    <th scope="col">Temp. Agua</th>
                    <th scope="col">Presion</th>
                    <th scope="col">Indice UV</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="row in muestreo" :key="row.time" @click="getMuestreo(item)">
                    <td>{{row.date}}</td>
                    <td>{{row.time}}</td>
                    <td>{{row.latitude}}</td>
                    <td>{{row.longitude}}</td>
                    <td>{{row.tempInternal}}</td>
                    <td>{{row.tempAir}}</td>
                    <td>{{row.tempWater}}</td>
                    <td>{{row.pressure}}</td>
                    <td>{{row.uv}}</td>
                  </tr>
                </tbody>
              </table>
            </div>

          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Plotly } from 'vue-plotly'

export default {
  layout: 'tablero',

  data() {
    return {
      muestreo: [],
      ficha: {},
      agua:[{
        x: [],
        y: [],
        type:"scatter",
        xlabel: 'tiempo',
        name: 'Temp. (C) - Agua',
        showlegend: true
      }],
      aire:[{
        x: [],
        y: [],
        type:"scatter",
        name: 'Temp. (C) - Aire',
        showlegend: true
      }],
      uv:[{
        x: [],
        y: [],
        type:"scatter",
        name: 'Indice UV',
        showlegend: true
      }],
      presion:[{
        x: [],
        y: [],
        type:"scatter",
        name: 'Presión (kPa)',
        showlegend: true
      }],
      layoutAgua:{
        title: "Temperatura del agua"
      },
      layoutAire:{
        title: "Temperatura del aire"
      },
      layoutUv:{
        title: "Indice UV"
      },
      layoutPresion:{
        title: "Presión"
      },
      layoutCombinado:{
        title: "Superposición de variables"
      }
    }
  },

  components: {
    Plotly
  },

  computed: {
    grafica_combinada: function() {
      let data = []
      data.push(this.agua[0])
      data.push(this.aire[0])
      data.push(this.uv[0])
      data.push(this.presion[0])
      return data
    }
  },

  asyncData(context) {
    return {
      project: "SPC"
    };
  },

  async mounted() {
    let env = require("~/const/env.json");
    let token = localStorage.getItem("token")
    let userId = localStorage.getItem("userId")
    let url = `${env.api_host}/usuario/${userId}/accessTokens?access_token=${token}`;
    let apiToken = ""
    let idMuestreo = this.$route.params.id
    let self = this

    if (!token) {
      this.$router.push('/auth/login')
    }

    const actualToken = await this.$axios.$get(url)
    
    actualToken.forEach(element => {
      if (element.id === token) {
        apiToken = token
        console.log('token-presente')
      }
    })

    if (token != apiToken) {
      localStorage.setItem("token", null)
      localStorage.setItem("userId", null)
      this.$router.push("/auth/login");
    }

    let urlMuestreo = `${env.api_host}/estudio/${idMuestreo}?access_token=${token}`
    await this.$axios.$get(urlMuestreo)
    .then(function(response) {
      self.muestreo = response.data

      response.data.forEach(element => {
        self.agua[0].x.push(element.date + '-' + element.time)
        self.aire[0].x.push(element.date + '-' + element.time)
        self.uv[0].x.push(element.date + '-' + element.time)
        self.presion[0].x.push(element.date + '-' + element.time)
        self.agua[0].y.push(element.tempWater)
        self.aire[0].y.push(element.tempAir)
        self.uv[0].y.push(element.uv)
        self.presion[0].y.push(element.pressure)
      })
    })
    .catch(function(e) {
      if (e.response) {
        let error = e.response.data.error;
        let detalles = error.details;
        console.log(error.statusCode)
      } else {
        console.log(e);
      }
    })

    let urlFicha = `${env.api_host}/estudio/${idMuestreo}/ficha?access_token=${token}`
    await this.$axios.$get(urlFicha)
    .then(function(response) {
      self.ficha = response
    })
    .catch(function(e) {
      if (e.response) {
        let error = e.response.data.error;
        let detalles = error.details;
        console.log(error.statusCode);
      } else {
        console.log(e);
      }
    })

  },

  methods: {
    getEstudios(host, token) {
      
    }

  }
};
</script>

<style scoped>
</style>
