<template>
  <div>
    <nav aria-label="breadcrumb">
      <ol class="breadcrumb">
        <li class="breadcrumb-item active" aria-current="page">Cambiar la contraseña</li>
      </ol>
    </nav>
    <div class="app flex-row">
      <div class="container">
        <div class="row">
          <div class="col-sm-8 col-md-6">
            <div class="card mx-4">
              <div class="card-body p-4">
                <form @submit.prevent="onSubmit">
                  <p class="text-muted">Complete todos los campos para cambiar su contraseña</p>
                  <div role="group" class="input-group mb-3">
                    <div class="input-group-prepend">
                      <div class="input-group-text">
                        <i class="fas fa-key"></i>
                      </div>
                    </div>
                    <input
                      v-model="userData.oldPassword"
                      type="password"
                      placeholder="Contraseña actual"
                      autocomplete="old-password"
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
                      v-model="userData.newPassword"
                      type="password"
                      placeholder="Contraseña nueva"
                      autocomplete="new-password"
                      class="form-control form-control"
                      id="_newPassword"
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
                      v-model="userData.newPassword_confirm"
                      type="password"
                      placeholder="Repita su contraseña nueva"
                      autocomplete="new-password"
                      class="form-control form-control"
                      id="_newPassword_confirm"
                      required
                    />
                  </div>
                  <button type="submit" class="btn btn-primary btn-block">Cambiar contraseña</button>
                </form>
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
  layout: 'tablero',

  data() {
    return {
      userData: {
        oldPassword: "",
        newPassword: "",
        password_confirm: ""
      },
      error: {
        username: false,
        email: false
      }
    };
  },

  asyncData(context) {
    return {
      project: "SPC"
    };
  },

  mounted() {
    this.checkToken()
  },

  methods: {
    async checkToken() {
      let env = require("~/const/env.json");
      let token = localStorage.getItem("token")
      let userId = localStorage.getItem("userId")
      let url = `${env.api_host}/usuario/${userId}/accessTokens?access_token=${token}`;
      let apiToken = ""

      if (!token) {
        this.$router.push('/auth/login')
      }

      const actualToken = await this.$axios.$get(url)
      
      actualToken.forEach(element => {
        if (element.id === token) {
          apiToken = token
          console.log('token-pass')
        }
      });

      if (token != apiToken) {
        localStorage.setItem("token", null)
        localStorage.setItem("userId", null)
        this.$router.push("/auth/login");
      }
    },

    onSubmit(params) {
      let userData = this.userData;
      let env = require("~/const/env.json");
      let token = localStorage.getItem("token")
      let url = env.api_host + "/usuario/change-password";
      let self = this;

      this.$axios({
        method: "post",
        url: url,
        mode: "no-cors",
        headers: {
          "Access-Control-Allow-Origin": "*",
          "Content-Type": "application/json"
        },
        params: {
          "access_token": token
        },
        withCredentials: false,
        data: {
          oldPassword: userData.oldPassword,
          newPassword: userData.newPassword
        }
      })

      .then(function(response) {
        self.$toast.success("La contraseña fue cambiada de manera exitosa.", {
          duration: 5000,
          iconPack: "fontawesome",
          icon: "check"
        });

        localStorage.setItem("token", null)
        localStorage.setItem("userId", null)
        self.$router.push("/auth/login");
      })

      .catch(function(e) {
        if (e.response) {
          let error = e.response.data.error;
          let detalles = error.details;
          
          self.$toast.error("Error, verifique los datos", {
            duration: 3500,
            iconPack: "fontawesome",
            icon: "times"
          });
          
        } else {
          console.log(e);
        }
      });
    }
  }
};
</script>
