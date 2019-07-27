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
                  <p class="text-muted">Ingrese su email para restablecer la contraseña.</p>
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
                  </div>
                  <button type="submit" class="btn btn-primary btn-block">Enviar</button>
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
      userData: {
        email: ""
      },
      error: {
        email: false
      }
    };
  },

  asyncData(context) {
    return {
      project: "SPC"
    };
  },

  methods: {
    onSubmit(params) {
      let userData = this.userData;
      let env = require("~/const/env.json");
      let url = env.api_host + "/usuario/reset";
      let self = this;

      this.$axios({
        method: "post",
        url: url,
        mode: "no-cors",
        headers: {
          "Access-Control-Allow-Origin": "*",
          "Content-Type": "application/json"
        },
        withCredentials: false,
        data: {
          email: userData.email
        }
      })

        .then(function(response) {
          self.$toast.success(
            "Consulte su bandeja de entrada, si el email esta registrado recibira un correo.",
            {
              duration: 10000,
              iconPack: "fontawesome",
              icon: "check"
            }
          );

          self.$router.push("/auth/login");
        })

        .catch(function(e) {
          if (e.response) {
            let error = e.response.data.error;
            let detalles = error.details;

            self.$toast.error(
              "Error, no es posible realizar la operación en este momento",
              {
                duration: 4000,
                iconPack: "fontawesome",
                icon: "times"
              }
            );
          } else {
            console.log(e);
          }
        });
    },

    login(params) {
      this.$router.push("/auth/login");
    }
  }
};
</script>
