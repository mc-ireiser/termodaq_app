<template>
  <div v-if="ready">
    <nav aria-label="breadcrumb">
      <ol class="breadcrumb">
        <li v-if="ficha.titulo" class="breadcrumb-item active" aria-current="page">
          Muestreo: {{ficha.titulo}} - {{ficha.lugar}} -
          <a
            href="/tablero/estudios/listado"
          >Volver al listado</a>
        </li>
        <li v-else class="breadcrumb-item active" aria-current="page">
          Muestreo: {{muestreo.id}} -
          <a href="/tablero/estudios/listado">Volver al listado</a>
        </li>
      </ol>
    </nav>
    <div class="app flex-row">
      <div class="container">
        <div class="row">
          <div class="col-sm-12">

            <div v-if="!ficha.titulo" class="mx-4 alert alert-primary" role="alert">
              Presione editar y proceda a completar los campos para crear una ficha de estudio.
            </div>

            <div v-if="!edit" class="mx-4 mb-4 pl-2 text-right">
              <button type="button" class="btn btn-warning ml-2" data-toggle="modal" data-target="#modalEditar">
                <span class="fas fa-edit"></span> Editar</button>
              <button type="button" class="btn btn-danger ml-2" data-toggle="modal" data-target="#modalEliminar">
                <span class="fas fa-trash"></span> Eliminar</button>
            </div>

            <div v-if="ficha.titulo">
              <div class="card mx-4 pl-2">
                <h5 class="my-2">Titulo: {{ficha.titulo}}</h5>
                <h6>Lugar: {{ficha.lugar}}</h6>
              </div>

              <div class="card mx-4 pl-2">
                <h5 class="my-2">Descripcion</h5>
                <p class="mt-2">{{ficha.descripcion}}</p>
              </div>
            </div>

            <div v-if="!edit" class="card mx-4 p-2">
              <h5 class="my-2">Investigadores</h5>
              <div v-if="perfil" class="mt-2">
                <h6>{{userData.nombre}} {{userData.apellido}}</h6>
                <h6>{{userData.institucion}}</h6>
                <h6>{{userData.pais}}</h6>
                <hr>
              </div>
              <div v-else>
                <div class="alert alert-primary" role="alert">
                  Complete su perfil.
                </div>
              </div>
            </div>            

            <div v-if="!edit" class="card mx-4 p-2">
              <h5 class="mt-2">Resultados Generales</h5>
              <hr>
              <div class="row">
                <div class="col-sm">
                  <div class="card-body">
                    <div class="h4 mb-0">{{aguaAvg.toFixed(4)}} ℃</div>
                    <small class="text-muted text-uppercase font-weight-bold">Avg. Temp. Agua</small>
                    <br />
                    <br />
                    <small class="text-muted text-uppercase font-weight-bold">MIN {{aguaMin}} ℃</small>
                    <br />
                    <small class="text-muted text-uppercase font-weight-bold">MAX {{aguaMax}} ℃</small>
                    <div class="progress-xs mt-3 mb-0 progress">
                      <div
                        role="progressbar"
                        :aria-valuemin="aguaMin"
                        :aria-valuemax="aguaMax"
                        :aria-valuenow="aguaAvg"
                        class="progress-bar bg-danger"
                        style="width: 100%;"
                      ></div>
                    </div>
                  </div>
                </div>
                <div class="col-sm">
                  <div class="card-body">
                    <div class="h4 mb-0">{{aireAvg.toFixed(4)}} ℃</div>
                    <small class="text-muted text-uppercase font-weight-bold">Avg. Temp. Ambiente</small>
                    <br />
                    <br />
                    <small class="text-muted text-uppercase font-weight-bold">MIN {{aireMin}} ℃</small>
                    <br />
                    <small class="text-muted text-uppercase font-weight-bold">MAX {{aireMax}} ℃</small>
                    <div class="progress-xs mt-3 mb-0 progress">
                      <div
                        role="progressbar"
                        :aria-valuemin="aireMin"
                        :aria-valuemax="aireMax"
                        :aria-valuenow="aireAvg"
                        class="progress-bar bg-primary"
                        style="width: 100%;"
                      ></div>
                    </div>
                  </div>
                </div>
                <div class="col-sm">
                  <div class="card-body">
                    <div class="h4 mb-0">{{presionAvg.toFixed(4)}} kPa</div>
                    <small class="text-muted text-uppercase font-weight-bold">Avg. Presión</small>
                    <br />
                    <br />
                    <small class="text-muted text-uppercase font-weight-bold">MIN {{presionMin}} kPa</small>
                    <br />
                    <small class="text-muted text-uppercase font-weight-bold">MAX {{presionMax}} kPa</small>
                    <div class="progress-xs mt-3 mb-0 progress">
                      <div
                        role="progressbar"
                        :aria-valuemin="presionMin"
                        :aria-valuemax="presionMax"
                        :aria-valuenow="presionAvg"
                        class="progress-bar bg-success"
                        style="width: 100%;"
                      ></div>
                    </div>
                  </div>
                </div>
                <div class="col-sm">
                  <div class="card-body">
                    <div class="h4 mb-0">{{uvAvg}} UV</div>
                    <small class="text-muted text-uppercase font-weight-bold">Avg. Indice UV</small>
                    <br />
                    <br />
                    <small class="text-muted text-uppercase font-weight-bold">MIN {{uvMin}} UV</small>
                    <br />
                    <small class="text-muted text-uppercase font-weight-bold">MAX {{uvMax}} UV</small>
                    <div class="progress-xs mt-3 mb-0 progress">
                      <div
                        role="progressbar"
                        :aria-valuemin="uvMin"
                        :aria-valuemax="uvMax"
                        :aria-valuenow="uvAvg"
                        class="progress-bar bg-warning"
                        style="width: 100%;"
                      ></div>
                    </div>
                  </div>
                </div>
              </div>
            </div>

            <div v-if="!edit" class="card mx-4 p-2" style="overflow-x: scroll;">
              <h5 class="my-2">Resultados por día</h5>
              <small>Aire = Temp Ambiente (℃)</small>
              <small>Agua = Temp Agua (℃)</small>
              <small>Presión (kPa)</small>
              <small>UV (Indice UV)</small>
              <table class="table table-hover mt-4">
                <thead>
                  <tr>
                    <th scope="col">Fecha<br>(A-M-D)</th>
                    <th scope="col">Aire<br>(min)</th>
                    <th scope="col">Aire<br>(max)</th>
                    <th scope="col">Aire<br>(avg)</th>
                    <th scope="col">Agua<br>(min)</th>
                    <th scope="col">Agua<br>(max)</th>
                    <th scope="col">Agua<br>(avg)</th>
                    <th scope="col">Presión<br>(min)</th>
                    <th scope="col">Presión<br>(max)</th>
                    <th scope="col">Presión<br>(avg)</th>
                    <th scope="col">UV<br>(min)</th>
                    <th scope="col">UV<br>(max)</th>
                    <th scope="col">UV<br>(avg)</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="obj in groupData" :key="obj.id">
                    <td>{{ obj.date }}</td>
                    
                    <td>{{ Math.min(...obj.data.tempAir) }}</td>
                    <td>{{ Math.max(...obj.data.tempAir) }}</td>
                    <td>{{ (obj.data.tempAir.reduce((a, b) => a + b, 0) / obj.data.tempAir.length).toFixed(4) }}</td>
                    
                    <td>{{ Math.min(...obj.data.tempWater) }}</td>
                    <td>{{ Math.max(...obj.data.tempWater) }}</td>
                    <td>{{ (obj.data.tempWater.reduce((a, b) => a + b, 0) / obj.data.tempWater.length).toFixed(4) }}</td>
                    
                    <td>{{ Math.min(...obj.data.pressure) }}</td>
                    <td>{{ Math.max(...obj.data.pressure) }}</td>
                    <td>{{ (obj.data.pressure.reduce((a, b) => a + b, 0) / obj.data.pressure.length).toFixed(4) }}</td>

                    <td>{{ Math.min(...obj.data.uv) }}</td>
                    <td>{{ Math.max(...obj.data.uv) }}</td>
                    <td>{{ parseInt(obj.data.uv.reduce((a, b) => a + b, 0) / obj.data.uv.length).toFixed(0) }}</td>
                  </tr>
                </tbody>
              </table>
            </div>

            <div v-if="!edit" class="d-sm-down-none">
              <no-ssr>
                <div v-if="muestreo.data" class="card mx-4 p-2" style="height: 50vh">
                  <h5 class="my-2 mb-4">Area de estudio</h5>
                  <l-map
                    :zoom="15"
                    :center="[muestreo.data[0].latitude || 0, muestreo.data[0].longitude || 0]"
                  >
                    <l-tile-layer url="http://{s}.tile.osm.org/{z}/{x}/{y}.png"></l-tile-layer>
                    <l-marker
                      v-for="row in muestreo.data"
                      :lat-lng="[row.latitude, row.longitude]"
                      :key="row.id"
                    ></l-marker>
                  </l-map>
                </div>
              </no-ssr>

              <no-ssr>
                <div class="card mx-4 pl-2">
                  <Plotly :data="agua" :layout="layoutAgua" :display-mode-bar="true"></Plotly>
                </div>
              </no-ssr>

              <no-ssr>
                <div class="card mx-4 pl-2">
                  <Plotly :data="aire" :layout="layoutAire" :display-mode-bar="true"></Plotly>
                </div>
              </no-ssr>

              <no-ssr>
                <div class="card mx-4 pl-2">
                  <Plotly :data="uv" :layout="layoutUv" :display-mode-bar="true"></Plotly>
                </div>
              </no-ssr>

              <no-ssr>
                <div class="card mx-4 pl-2">
                  <Plotly :data="presion" :layout="layoutPresion" :display-mode-bar="true"></Plotly>
                </div>
              </no-ssr>

              <no-ssr>
                <div id="myDiv" class="card mx-4 pl-2">
                  <Plotly
                    :data="grafica_combinada"
                    :layout="layoutCombinado"
                    :display-mode-bar="true"
                  ></Plotly>
                </div>
              </no-ssr>

              <div class="card mx-4 p-4 d-md-down-none" style="overflow-x: scroll;">
                <h5 class="my-2 mb-4">Data RAW</h5>
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

            <!-- Modal Editar -->
            <div class="modal fade" id="modalEditar" tabindex="-1" role="dialog" aria-labelledby="modalEditarLabel" aria-hidden="true">
              <div class="modal-dialog" role="document">
                <div class="modal-content">
                  <div class="modal-header">
                    <h5 class="modal-title" id="modalEliminarLabel">Ficha</h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                      <span aria-hidden="true">&times;</span>
                    </button>
                  </div>
                  <div class="modal-body">
                    <form>
                      <div class="form-group">
                        <label for="titulo">Titulo</label>
                        <input v-model="ficha.titulo" type="text" class="form-control" id="titulo" placeholder="Muestreo TDAQ-001" required>
                      </div>
                      <div class="form-group">
                        <label for="lugar">Lugar</label>
                        <input v-model="ficha.lugar" type="text" class="form-control" id="lugar" placeholder="Río TDAQ" required>
                      </div>
                      <div class="form-group">
                        <label for="descripcion">Descripcion</label>
                        <textarea v-model="ficha.descripcion" class="form-control" id="descripcion" rows="3"></textarea>
                      </div>
                    </form>
                  </div>
                  <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-dismiss="modal">Cancelar</button>
                    <button type="button" class="btn btn-primary" @click="setFicha()">Guardar</button>
                  </div>
                </div>
              </div>
            </div>

            <!-- Modal Eliminar -->
            <div class="modal fade" id="modalEliminar" tabindex="-1" role="dialog" aria-labelledby="modalEliminarLabel" aria-hidden="true">
              <div class="modal-dialog" role="document">
                <div class="modal-content">
                  <div class="modal-header">
                    <h5 class="modal-title" id="modalEliminarLabel">Eliminar estudio</h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                      <span aria-hidden="true">&times;</span>
                    </button>
                  </div>
                  <div class="modal-body">
                    ¿Está seguro de eliminar este estudio de forma permanente?
                  </div>
                  <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-dismiss="modal">Cancelar</button>
                    <button type="button" class="btn btn-danger" @click="eliminarEstudio()">Eliminar</button>
                  </div>
                </div>
              </div>
            </div>
              
          </div>
        </div>
      </div>
    </div>
  </div>
  <div v-else>
    <div class="alert alert-primary" role="alert">
      Cargando datos.
    </div>
  </div>
</template>

<script>
import { Plotly } from "vue-plotly";

export default {
  layout: "tablero",

  data() {
    return {
      ready: false,
      perfil: false,
      edit: false,
      muestreos_count: 0,
      groupData: [],
      userData: {
        nombre: "",
        apellido: "",
        bio: "",
        telefono: "",
        institucion: "",
        pais: "",
        id: "",
        userId: ""
      },
      muestreo: [],
      ficha: {
        titulo: "",
        lugar: "",
        descripcion: ""
      },
      agua: [
        {
          x: [],
          y: [],
          type: "scatter",
          xlabel: "tiempo",
          name: "Temp. Agua (C)",
          showlegend: true
        }
      ],
      aire: [
        {
          x: [],
          y: [],
          type: "scatter",
          name: "Temp. Aire (C)",
          showlegend: true
        }
      ],
      uv: [
        {
          x: [],
          y: [],
          type: "scatter",
          name: "Indice UV",
          showlegend: true
        }
      ],
      presion: [
        {
          x: [],
          y: [],
          type: "scatter",
          name: "Presión (kPa)",
          showlegend: true
        }
      ],
      layoutAgua: {
        title: "Temperatura del agua",
        yaxis: {
          title: "Temp. (C)"
        },
        xaxis: {
          title: "Tiempo",
          automargin: true
        }
      },
      layoutAire: {
        title: "Temperatura del aire",
        yaxis: {
          title: "Temp. (C)"
        },
        xaxis: {
          title: "Tiempo",
          automargin: true
        }
      },
      layoutUv: {
        title: "Indice UV",
        yaxis: {
          title: "Indice UV"
        },
        xaxis: {
          title: "Tiempo",
          automargin: true
        }
      },
      layoutPresion: {
        title: "Presión",
        yaxis: {
          title: "Presión (kPa)"
        },
        xaxis: {
          title: "Tiempo",
          automargin: true
        }
      },
      layoutCombinado: {
        title: "Superposición de variables",
        xaxis: {
          title: "Tiempo",
          automargin: true
        }
      }
    };
  },

  components: {
    Plotly
  },

  computed: {
    grafica_combinada: function() {
      let data = [];
      data.push(this.agua[0]);
      data.push(this.aire[0]);
      data.push(this.uv[0]);
      data.push(this.presion[0]);
      return data;
    },

    aguaMin: function() {
      let x = this.agua[0].y;
      return Math.min(...x);
    },

    aguaMax: function() {
      let x = this.agua[0].y;
      return Math.max(...x);
    },

    aguaAvg() {
      let x = this.agua[0].y;
      return x.reduce((a, b) => a + b, 0) / x.length;
    },

    aireMin: function() {
      let x = this.aire[0].y;
      return Math.min(...x);
    },

    aireMax: function() {
      let x = this.aire[0].y;
      return Math.max(...x);
    },

    aireAvg() {
      let x = this.aire[0].y;
      return x.reduce((a, b) => a + b, 0) / x.length;
    },

    presionMin: function() {
      let x = this.presion[0].y;
      return Math.min(...x);
    },

    presionMax: function() {
      let x = this.presion[0].y;
      return Math.max(...x);
    },

    presionAvg() {
      let x = this.presion[0].y;
      return x.reduce((a, b) => a + b, 0) / x.length;
    },

    uvMin: function() {
      let x = this.uv[0].y;
      return Math.min(...x);
    },

    uvMax: function() {
      let x = this.uv[0].y;
      return Math.max(...x);
    },

    uvAvg() {
      let x = this.uv[0].y;
      return parseInt(x.reduce((a, b) => a + b, 0) / x.length);
    }
  },

  asyncData(context) {
    return {
      project: "SPC"
    };
  },

  async mounted() {
    let env = require("~/const/env.json");
    let token = localStorage.getItem("token");
    let userId = localStorage.getItem("userId");
    let urlToken = `${env.api_host}/usuario/${userId}/accessTokens?access_token=${token}`;
    let apiToken = "";
    let idMuestreo = this.$route.params.id;
    let self = this;

    if (!token) {
      this.$router.push("/auth/login");
    }

    let urlEstudios = `${env.api_host}/estudio/${idMuestreo}?filter[include]=ficha&access_token=${token}`;
    await this.$axios
      .$get(urlEstudios)

      .then(async function(response) {
        self.muestreo = response;
        
        if (response.ficha) {
          self.ficha = response.ficha;
        }

        response.data.forEach(element => {
          self.agua[0].x.push(element.date + " " + element.time);
          self.aire[0].x.push(element.date + " " + element.time);
          self.uv[0].x.push(element.date + " " + element.time);
          self.presion[0].x.push(element.date + " " + element.time);
          self.agua[0].y.push(element.tempWater);
          self.aire[0].y.push(element.tempAir);
          self.uv[0].y.push(element.uv);
          self.presion[0].y.push(element.pressure);
        });

 //       let groupedResults = _.groupBy(results, (result) => moment(result['Date'], 'DD/MM/YYYY').startOf('isoWeek'));


        // this gives an object with dates as keys
        const groups = await response.data.reduce((groups, data) => {
          // const date = data.date + ' ' + data.time;
          const date = data.date;
          if (!groups[date]) {
            groups[date] = {
              'tempAir': [],
              'tempWater': [],
              'pressure': [],
              'uv': [],
            };
          }
          console.log(groups[date])
          groups[date].tempAir.push(data.tempAir);
          groups[date].tempWater.push(data.tempWater);
          groups[date].pressure.push(data.pressure);
          groups[date].uv.push(data.uv);
          return groups;
        }, {});

        // Edit: to add it in the array format instead
        const groupArrays = await Object.keys(groups).map((date) => {
          return {
            date,
            data: groups[date]
          };
        });

        self.groupData = groupArrays

      })

      .catch(function(e) {
        if (e.response) {
          let error = e.response.data.error;
          let detalles = error.details;
          console.log(error.statusCode);
        } else {
          console.log(e);
        }
      });

    let urlPerfil = `${env.api_host}/usuario/${userId}/perfil?access_token=${token}`;
    await this.$axios
      .$get(urlPerfil)

      .then(function(response) {
        self.perfil = true;
        self.userData = response;
      })

      .catch(function(e) {
        if (e.response) {
          let error = e.response.data.error;
          let detalles = error.details;
          console.log(error.statusCode);

          if (error.code == "MODEL_NOT_FOUND") {
            self.perfil = false;
          }
        } else {
          console.log(e);
        }
      });

    await this.$axios
      .$get(urlToken)

      .then(function(response) {
        response.forEach(element => {
          if (element.id === token) {
            apiToken = token;
            console.log("token-presente");
          }
        });

        if (token != apiToken) {
          localStorage.setItem("token", "");
          localStorage.setItem("userId", "");
          self.$router.push("/auth/login");
        }
      })

      .catch(function(e) {
        if (e.response) {
          let error = e.response.data.error;
          let detalles = error.details;

          if ((error.statusCode = 401)) {
            console.log("Acceso no autorizado, inicie sesión nuevamente");
          }

          self.$toast.error("Acceso no autorizado, inicie sesión nuevamente", {
            duration: 5000,
            iconPack: "fontawesome",
            icon: "check"
          });

          localStorage.setItem("token", "");
          localStorage.setItem("userId", "");
          self.$router.push("/auth/login");
        } else {
          console.log(e);
        }
      });
    
    this.ready = true;
  },

  methods: {
    async setFicha() {
      let env = require("~/const/env.json");
      let token = localStorage.getItem("token");
      let userId = localStorage.getItem("userId");
      let idMuestreo = this.$route.params.id;
      let urlFicha = `${env.api_host}/estudio/${idMuestreo}/ficha?access_token=${token}`;
      let self = this;

      if (!this.ficha.titulo || this.ficha.lugar) {
        this.$toast.info("Campos titulo y lugar son requeridos.", {
          duration: 3500,
          iconPack: "fontawesome",
          icon: "check"
        });
        return
      }

      if (this.ficha.id) {
        await this.$axios

        .$put(urlFicha, self.ficha)

        .then(function(response) {
          self.ficha = response;
          self.$toast.success("Ficha editada correctamente.", {
            duration: 3500,
            iconPack: "fontawesome",
            icon: "check"
          });
        })

        .catch(function(e) {
          if (e.response) {
            let error = e.response.data.error;
            let detalles = error.details;
            console.log(error.statusCode);
            self.$toast.error("Error editando ficha.", {
              duration: 3500,
              iconPack: "fontawesome",
              icon: "check"
            });

          } else {
            console.log(e);
          }
        });

      } else {
        await this.$axios

        .$post(urlFicha, self.ficha)

        .then(function(response) {
          self.ficha = response;
          self._ficha = true;
          self.$toast.success("Ficha creada de manera correcta.", {
            duration: 3500,
            iconPack: "fontawesome",
            icon: "check"
          });
        })

        .catch(function(e) {
          if (e.response) {
            let error = e.response.data.error;
            let detalles = error.details;
            console.log(error.statusCode);

            self.ficha.titulo = '';
            self.ficha.lugar = '';
            self.ficha.descripcion = '';

            self.$toast.error("Error creando ficha.", {
              duration: 3500,
              iconPack: "fontawesome",
              icon: "check"
            });
          } else {
            console.log(e);
          }
        });
      }

      this.edit = false
      $('#modalEditar').modal('toggle')
    },

    async eliminarEstudio() {
      let env = require("~/const/env.json");
      let token = localStorage.getItem("token");
      let userId = localStorage.getItem("userId");
      let idMuestreo = this.$route.params.id;
      let urlDeleteEstudio = `${env.api_host}/estudio/${idMuestreo}?access_token=${token}`;
      let urlDeleteFicha = `${env.api_host}/estudio/${idMuestreo}/ficha?access_token=${token}`;
      let self = this;

      if (this.ficha.id) {
      
        await this.$axios

        .$delete(urlDeleteFicha)

        .then(function(response) {
          self.$toast.success("Ficha eliminada.", {
            duration: 3500,
            iconPack: "fontawesome",
            icon: "check"
          });

          $('#modalEliminar').modal('toggle')
          self.$router.push("/tablero/estudios/listado");
        })

        .catch(function(e) {
          if (e.response) {
            let error = e.response.data.error;
            let detalles = error.details;
            console.log(error.statusCode);
            self.$toast.success("Error eliminando ficha", {
              duration: 3500,
              iconPack: "fontawesome",
              icon: "check"
            });

          } else {
            console.log(e);
          }
        });
      }

      await this.$axios

      .$delete(urlDeleteEstudio)

      .then(function(response) {
        self.$toast.success("Estudio eliminado.", {
          duration: 3500,
          iconPack: "fontawesome",
          icon: "check"
        });

        $('#modalEliminar').modal('toggle')
        self.$router.push("/tablero/estudios/listado");
      })

      .catch(function(e) {
        if (e.response) {
          let error = e.response.data.error;
          let detalles = error.details;
          console.log(error.statusCode);
          self.$toast.success("Error eliminando estudio", {
            duration: 3500,
            iconPack: "fontawesome",
            icon: "check"
          });

        } else {
          console.log(e);
        }
      });

    }
  }
};
</script>
