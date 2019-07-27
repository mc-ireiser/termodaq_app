<template>
  <div>
    <nav aria-label="breadcrumb">
      <ol class="breadcrumb">
        <li class="breadcrumb-item active" aria-current="page">Estudios realizados</li>
      </ol>
    </nav>
    <div class="app flex-row">
      <div class="container">
        <div class="row">
          <div class="col-sm-12">
            <div class="card mx-4">
              <table class="table table-hover">
                <thead>
                  <tr>
                    <th scope="col">Titulo</th>
                    <th scope="col">Lugar</th>
                    <th scope="col">Descripción</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="item in fichas" :key="item.id" @click="getMuestreo(item)">
                    <td>{{item.titulo}}</td>
                    <td>{{item.lugar}}</td>
                    <td>{{item.descripcion}}</td>
                  </tr>
                </tbody>
              </table>
            </div>
            <div class="card mx-4">
              <table class="table table-hover">
                <thead>
                  <tr>
                    <th scope="col">Estudios sin ficha</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="item in estudios_sficha" :key="item" @click="getMuestreoSf(item)">
                    <td>{{item}}</td>
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
export default {
  layout: "tablero",

  data() {
    return {
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
