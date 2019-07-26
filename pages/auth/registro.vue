<template>
  <div>
    <div class="app flex-row align-items-center">
      <div class="container">
        <div class="row justify-content-center">
          <div class="col-sm-8 col-md-6">
            <div class="card mx-4">
              <div class="card-body p-4">
                <form @submit.prevent="onSubmit">
                  <h1>Registro</h1>
                  <p class="text-muted">Complete todos los campos para crea su cuenta</p>
                  <div role="group" class="input-group mb-3">
                    <div class="input-group-prepend">
                      <div class="input-group-text">
                        <i class="fas fa-user"></i>
                      </div>
                    </div>
                    <input
                      v-model="userData.username"
                      type="text"
                      placeholder="Usuario"
                      autocomplete="usuario"
                      class="form-control form-control"
                      id="_usuario"
                      required
                    />        
                    <small v-if="error.username" class="form-text text-danger">
                      El nombre de usuario ingresado ya se encuentra registrado en el sistema. Por favor intente nuevamente utilizando un nombre de usuario distinto.
                    </small>
                  </div>
                  <div role="group" class="input-group mb-3">
                    <div class="input-group-prepend">
                      <div class="input-group-text">
                        <i class="fas fa-envelope"></i>
                      </div>
                    </div>
                    <input
                      v-model="userData.email"
                      type="email"
                      placeholder="Email"
                      autocomplete="email"
                      class="form-control form-control"
                      id="_email"
                      required
                    />
                    <small v-if="error.email" class="form-text text-danger">
                      El email ingresado ya se encuentra registrado en el sistema. Por favor intente nuevamente utilizando un email distinto.
                    </small>
                  </div>
                  <div role="group" class="input-group mb-3">
                    <div class="input-group-prepend">
                      <div class="input-group-text">
                        <i class="fas fa-key"></i>
                      </div>
                    </div>
                    <input
                      v-model="userData.password"
                      type="password"
                      placeholder="Contraseña"
                      autocomplete="new-password"
                      class="form-control form-control"
                      id="_password"
                      required
                    />
                  </div>
                  <div role="group" class="input-group mb-4">
                    <div class="input-group-prepend">
                      <div class="input-group-text">
                        <i class="fas fa-key"></i>
                      </div>
                    </div>
                    <input
                      v-model="userData.password_confirm"
                      type="password"
                      placeholder="Repita su contraseña"
                      autocomplete="new-password"
                      class="form-control form-control"
                      id="_password_confirm"
                      required
                    />
                  </div>
                  <button type="submit" class="btn btn-primary btn-block" :disabled="registroBtn">Crear una cuenta</button>
                </form>
              </div>
              <div class="card-footer p-4">
                <div class="row">
                  <div class="col-12">
                    <button type="button" @click="login" class="btn btn btn-secondary btn-block">
                      <span>Volver a Inicio de Sesión</span>
                    </button>
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

export default {

  data() {
    return {
      registroBtn: false,
      userData: {
        username: "",
        email: "",
        password: "",
        password_confirm: ""
      },
      error: {
        username: false,
        email: false
      }
    }
  },

  asyncData(context) {
    return {
      project: "SPC"
    }
  },

  async mounted() {
    let env = require("~/const/env.json");
    let token = localStorage.getItem("token")
    let userId = localStorage.getItem("userId")
    let url = `${env.api_host}/usuario/${userId}/accessTokens?access_token=${token}`;
    let apiToken = ""
    let self = this

    await this.$axios.$get(url)

    .then(function(response) {
      response.forEach(element => {
        if (element.id === token) {
          apiToken = token
          console.log('token-presente')
          self.$router.push("/tablero/estudios/listado")
        }
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
  },

  methods: {
    onSubmit(params) {
      this.registroBtn = true

      if (this.userData.password.trim() !== this.userData.password_confirm.trim()) {
        this.$toast.error('La contraseña no coincide, por favor verifique', {
          duration: 3500,
          iconPack: 'fontawesome',
          icon : 'times'
        })
        return
      }

      let userData = this.userData
      let env = require('~/const/env.json');
      let url = env.api_host + '/usuario'
      let self = this

      this.$axios({
        method: 'post',
        url: url,
        mode: 'no-cors',
        headers: {
          'Access-Control-Allow-Origin': '*',
          'Content-Type': 'application/json',
        },
        withCredentials: false,
        data: {
          username: userData.username,
          email: userData.email,
          password: userData.password
        }
      })

      .then(function (response) {
        self.registroBtn = false

        self.$toast.success('La cuenta fue creada de manera exitosa.', {
          duration: 3500,
          iconPack: 'fontawesome',
          icon : 'check'
        })
        
        self.$router.push('/auth/login')
      })

      .catch(function (e) {
        self.registroBtn = false

        if (e.response) {
          let error = e.response.data.error
          let detalles = error.details

          if (error.details.codes.username[0] == 'uniqueness') {
            self.error.username = true
          }

          if (error.details.codes.email[0] == 'uniqueness') {
            self.error.email = true
          }

          self.$toast.error('Error, verifique los datos', {
            duration: 3500,
            iconPack: 'fontawesome',
            icon : 'times'
          })

        } else {
          console.log(e)
        }
      }) 

    },

    login(params) {
      this.$router.push('/auth/login')
    }
  }
};
</script>

<style scoped>
</style>
