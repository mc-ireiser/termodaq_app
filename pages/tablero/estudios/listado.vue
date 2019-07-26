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
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  layout: 'tablero',

  data() {
    return {
      estudios: [],
      fichas: []
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

    let urlEstudios = `${env.api_host}/estudio?access_token=${token}`
    await this.$axios.$get(urlEstudios)
    
    .then(function(response) {
      response.forEach(element => {
        self.estudios.push(element.id)
      })
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

    this.getEstudios(env.api_host, token)
  },

  methods: {
    getEstudios(host, token) {
      let self = this
      this.estudios.forEach(id => {
        let urlFichas = `${host}/estudio/${id}/ficha?access_token=${token}`
        this.$axios.$get(urlFichas)
        .then(function(response) {
          self.fichas.push(response)
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
      })
    },

    getMuestreo(muestreo) {
      this.$router.push('/tablero/estudios/' + muestreo.userId)
    }

  }
};
</script>

<style scoped>
</style>
