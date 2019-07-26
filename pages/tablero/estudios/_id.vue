<template>
  <div v-if="muestreo">
    <nav aria-label="breadcrumb">
      <ol class="breadcrumb">
        <li v-if="ficha" class="breadcrumb-item active" aria-current="page">
          Muestreo: {{ficha.titulo}} - {{ficha.lugar}} -
          <a href="/tablero/estudios/listado">volver al listado</a>
        </li>
        <li v-else class="breadcrumb-item active" aria-current="page">
          Muestreo: N/A - N/A -
          <a href="/tablero/estudios/listado">volver al listado</a>
        </li>
      </ol>
    </nav>
    <div class="app flex-row">
      <div class="container">
        <div class="row">
          <div class="col-sm-12">

            <div v-if="ficha">
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
            </div>

            <div v-else>
              <div class="card mx-4 pl-2">
                <h5 class="my-2">Titulo: N/A</h5>
                <h6>Lugar: N/A</h6>              
              </div>

              <div class="card mx-4 pl-2">
                <h6 class="my-2">descripcion</h6>
                <p>N/A</p>
              </div>

              <div class="card mx-4 pl-2">
                <h6 class="my-2">Investigadores</h6>
                <p>N/A</p>
              </div>
            </div>

            <div class="card mx-4 pl-2">
              <div class="row">
                <div class="col-sm">
                  <div class="card-body">
                    <div class="h1 text-muted text-right mb-4">
                      <i class="icon-speedometer"></i>
                    </div>
                    <div class="h4 mb-0">{{aguaAvg}} ℃</div>
                    <small class="text-muted text-uppercase font-weight-bold">Avg. Temp. Agua</small><br><br>
                    <small class="text-muted text-uppercase font-weight-bold">MIN {{aguaMin}} ℃</small><br>
                    <small class="text-muted text-uppercase font-weight-bold">MAX {{aguaMax}} ℃</small>
                    <div class="progress-xs mt-3 mb-0 progress">
                      <div
                        role="progressbar"
                        :aria-valuemin="aguaMin"
                        :aria-valuemax="aguaMax"
                        :aria-valuenow="aguaAvg"
                        class="progress-bar bg-danger"
                        style="width: 100%;"
                      >
                      </div>
                    </div>
                  </div>
                </div>
                <div class="col-sm">
                  <div class="card-body">
                    <div class="h1 text-muted text-right mb-4">
                      <i class="icon-speedometer"></i>
                    </div>
                    <div class="h4 mb-0">{{aireAvg}} ℃</div>
                    <small class="text-muted text-uppercase font-weight-bold">Avg. Temp. Ambiente</small><br><br>
                    <small class="text-muted text-uppercase font-weight-bold">MIN {{aireMin}} ℃</small><br>
                    <small class="text-muted text-uppercase font-weight-bold">MAX {{aireMax}} ℃</small>
                    <div class="progress-xs mt-3 mb-0 progress">
                      <div
                        role="progressbar"
                        :aria-valuemin="aireMin"
                        :aria-valuemax="aireMax"
                        :aria-valuenow="aireAvg"
                        class="progress-bar bg-primary"
                        style="width: 100%;"
                      >
                      </div>
                    </div>
                  </div>
                </div>
                <div class="col-sm">
                  <div class="card-body">
                    <div class="h1 text-muted text-right mb-4">
                      <i class="icon-speedometer"></i>
                    </div>
                    <div class="h4 mb-0">{{presionAvg}} kPa</div>
                    <small class="text-muted text-uppercase font-weight-bold">Avg. Presión</small><br><br>
                    <small class="text-muted text-uppercase font-weight-bold">MIN {{presionMin}} kPa</small><br>
                    <small class="text-muted text-uppercase font-weight-bold">MAX {{presionMax}} kPa</small>
                    <div class="progress-xs mt-3 mb-0 progress">
                      <div
                        role="progressbar"
                        :aria-valuemin="presionMin"
                        :aria-valuemax="presionMax"
                        :aria-valuenow="presionAvg"
                        class="progress-bar bg-success"
                        style="width: 100%;"
                      >
                      </div>
                    </div>
                  </div>
                </div>
                <div class="col-sm">
                  <div class="card-body">
                    <div class="h1 text-muted text-right mb-4">
                      <i class="icon-speedometer"></i>
                    </div>
                    <div class="h4 mb-0">{{uvAvg}} UV</div>
                    <small class="text-muted text-uppercase font-weight-bold">Avg. Indice UV</small><br><br>
                    <small class="text-muted text-uppercase font-weight-bold">MIN {{uvMin}} UV</small><br>
                    <small class="text-muted text-uppercase font-weight-bold">MAX {{uvMax}} UV</small>
                    <div class="progress-xs mt-3 mb-0 progress">
                      <div
                        role="progressbar"
                        :aria-valuemin="uvMin"
                        :aria-valuemax="uvMax"
                        :aria-valuenow="uvAvg"
                        class="progress-bar bg-warning"
                        style="width: 100%;"
                      >
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>

            <no-ssr>
              <div v-if="muestreo.data" class="card mx-4 pl-2 p-4" style="height: 50vh">
                <l-map :zoom=15 :center="[muestreo.data[0].latitude || 0, muestreo.data[0].longitude || 0]">
                  <l-tile-layer url="http://{s}.tile.osm.org/{z}/{x}/{y}.png"></l-tile-layer>
                  <l-marker v-for="row in muestreo.data" :lat-lng="[row.latitude, row.longitude]" :key="row.id"></l-marker>
                </l-map>
              </div>
            </no-ssr>

            <no-ssr>
              <div class="card mx-4 pl-2">
                <Plotly :data="agua" :layout="layoutAgua" :display-mode-bar="false"></Plotly>
              </div>
            </no-ssr>
              

            <no-ssr>
              <div class="card mx-4 pl-2">
                <Plotly :data="aire" :layout="layoutAire" :display-mode-bar="false"></Plotly>
              </div>
            </no-ssr>

            <no-ssr>
              <div class="card mx-4 pl-2">
                <Plotly :data="uv" :layout="layoutUv" :display-mode-bar="false"></Plotly>
              </div>
            </no-ssr>

            <no-ssr>
              <div class="card mx-4 pl-2">
                <Plotly :data="presion" :layout="layoutPresion" :display-mode-bar="false"></Plotly>
              </div>
            </no-ssr>

            <no-ssr>
              <div id="myDiv" class="card mx-4 pl-2">
                <Plotly :data="grafica_combinada" :layout="layoutCombinado" :display-mode-bar="false"></Plotly>
              </div>
            </no-ssr>

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
                  <tr v-for="row in muestreo.data" :key="row.id">
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
      mapa: {
        type:'scattermapbox',
        lat:['45.5017'],
        lon:['-73.5673'],
        mode:'markers',
        marker: {
          size:14
        }
      },
      agua:[{
        x: [],
        y: [],
        type:"scatter",
        xlabel: 'tiempo',
        name: 'Temp. Agua (C)',
        showlegend: true
      }],
      aire:[{
        x: [],
        y: [],
        type:"scatter",
        name: 'Temp. Aire (C)',
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
        title: "Temperatura del agua",
        yaxis: {
          title: 'Temp. (C)',
        },
        xaxis: {
          title: 'Tiempo',
        }
      },
      layoutAire:{
        title: "Temperatura del aire",
        yaxis: {
          title: 'Temp. (C)',
        },
        xaxis: {
          title: 'Tiempo',
        }
      },
      layoutUv:{
        title: "Indice UV",
        yaxis: {
          title: 'Indice UV',
        },
        xaxis: {
          title: 'Tiempo',
        }
      },
      layoutPresion:{
        title: "Presión",
        yaxis: {
          title: 'Presión (kPa)',
        },
        xaxis: {
          title: 'Tiempo',
        }
      },
      layoutCombinado:{
        title: "Superposición de variables",
        xaxis: {
          title: 'Tiempo',
        }
      },
      layoutMapa: {
        autosize: true,
        hovermode:'closest',
        mapbox: {
          bearing:0,
          pitch:0,
          zoom:5
        }
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
    },

    aguaMin: function() {
      let x = this.agua[0].y
      return Math.min(...x)
    },

    aguaMax: function() {
      let x = this.agua[0].y
      return Math.max(...x)
    },

    aguaAvg() {
      let x = this.agua[0].y
      return x.reduce((a,b) => a + b, 0) / x.length
    },

    aireMin: function() {
      let x = this.aire[0].y
      return Math.min(...x)
    },

    aireMax: function() {
      let x = this.aire[0].y
      return Math.max(...x)
    },

    aireAvg() {
      let x = this.aire[0].y
      return x.reduce((a,b) => a + b, 0) / x.length
    },

    presionMin: function() {
      let x = this.presion[0].y
      return Math.min(...x)
    },

    presionMax: function() {
      let x = this.presion[0].y
      return Math.max(...x)
    },

    presionAvg() {
      let x = this.presion[0].y
      return x.reduce((a,b) => a + b, 0) / x.length
    },

    uvMin: function() {
      let x = this.uv[0].y
      return Math.min(...x)
    },

    uvMax: function() {
      let x = this.uv[0].y
      return Math.max(...x)
    },

    uvAvg() {
      let x = this.uv[0].y
      return parseInt(x.reduce((a,b) => a + b, 0) / x.length)
    },
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

    let urlEstudios = `${env.api_host}/estudio/${idMuestreo}?filter[include]=ficha&access_token=${token}`
    await this.$axios.$get(urlEstudios)
 
    .then(function(response) {
      self.muestreo = response
      self.ficha = response.ficha
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
  },

  methods: {

  }
};
</script>

<style scoped>
</style>
