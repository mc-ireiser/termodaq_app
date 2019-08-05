<template>
  <div v-if="ready">
    <nav aria-label="breadcrumb">
      <ol class="breadcrumb">
        <li class="breadcrumb-item active" aria-current="page">Listado de estudios realizados</li>
      </ol>
    </nav>
    <div class="app flex-row">
      <div class="container">
        <div class="row">
          <div class="col-sm-12">
            <div
              v-if="estudios.length === 0 & estudios_sficha.length === 0"
              class="mx-4 alert alert-primary"
              role="alert"
            >Usted aun no posee estudios registrados.</div>
            <div v-if="estudios.length > 0" class="card mx-4" style="overflow-x: scroll;">
              <table class="table table-hover">
                <thead>
                  <tr>
                    <th>Titulo</th>
                    <th class="d-md-down-none">Lugar</th>
                    <th class="d-md-down-none">Descripción</th>
                    <th></th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="item in fichas" :key="item.id">
                    <td style="max-width: 20vw;" class="text-truncate">{{item.titulo}}</td>
                    <td style="max-width: 20vw;" class="text-truncate d-md-down-none">{{item.lugar}}</td>
                    <td
                      style="max-width: 20vw;"
                      class="text-truncate d-md-down-none"
                    >{{item.descripcion}}</td>
                    <td>
                      <button
                        type="button"
                        class="btn btn-primary float-right"
                        @click="getMuestreo(item)"
                      >Ver</button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
            <div v-if="estudios_sficha.length > 0" class="card mx-4" style="overflow-x: scroll;">
              <table class="table table-hover">
                <thead>
                  <tr>
                    <th scope="col">Estudios sin ficha</th>
                    <th scope="col"></th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="item in estudios_sficha" :key="item">
                    <td>{{item}}</td>
                    <td>
                      <button
                        type="button"
                        class="btn btn-primary float-right"
                        @click="getMuestreoSf(item)"
                      >Ver</button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
  <div v-else>
    <div class="alert alert-primary" role="alert">Cargando datos.</div>
  </div>
</template>

<script>
export default {
  layout: "tablero",

  data() {
    return {
      ready: false,
      estudios: [],
      estudios_sficha: [],
      fichas: []
    };
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
    let self = this;

    if (!token) {
      this.$router.push("/auth/login");
    }

    let urlEstudios = `${env.api_host}/estudio?filter[where][userId]=${userId}&filter[fields][userId]=true&filter[fields][id]=true&filter[include]=ficha&access_token=${token}`;
    await this.$axios
      .$get(urlEstudios)

      .then(function(response) {
        response.forEach(element => {
          if (element.ficha) {
            self.fichas.push(element.ficha);
            self.estudios.push(element.id);
          } else {
            self.estudios_sficha.push(element.id);
          }
        });
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
    getMuestreo(muestreo) {
      this.$router.push("/tablero/estudios/" + muestreo.userId);
    },

    getMuestreoSf(muestreo) {
      this.$router.push("/tablero/estudios/" + muestreo);
    }
  }
};
</script>
