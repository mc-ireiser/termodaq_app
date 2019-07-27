<template>
  <div v-if="ready">
    <nav aria-label="breadcrumb">
      <ol class="breadcrumb">
        <li class="breadcrumb-item active" aria-current="page">Perfil de usuario</li>
      </ol>
    </nav>
    <div class="app flex-row">
      <div class="container">
        <div v-if="!perfil" class="mx-4 alert alert-primary" role="alert">Complete su Perfil.</div>
        <div class="row">
          <div class="col-sm-12 col-md-6">
            <div class="card mx-4">
              <div class="card-body p-4">
                <form @submit.prevent="onSubmit">
                  <div role="group" class="input-group mb-3">
                    <label for="nombre" class="col-sm-6 col-form-label">Nombre</label>
                    <input
                      type="text"
                      :readonly="!edit"
                      :class="edit? 'form-control' : 'form-control-plaintext' "
                      class
                      id="nombre"
                      v-model="userData.nombre"
                      value
                    />
                  </div>
                  <div role="group" class="input-group mb-3">
                    <label for="apellido" class="col-sm-6 col-form-label">Apellido</label>
                    <input
                      type="text"
                      :readonly="!edit"
                      :class="edit? 'form-control' : 'form-control-plaintext' "
                      id="apellido"
                      v-model="userData.apellido"
                      value
                    />
                  </div>
                  <div role="group" class="input-group mb-3">
                    <label for="bio" class="col-sm-6 col-form-label">Bio</label>
                    <textarea type="text" :readonly="!edit" :class="edit? 'form-control' : 'form-control-plaintext' " id="bio" v-model="userData.bio" value=""></textarea>
                    <!--
                      <textarea type="text" :readonly="!edit" :class="edit? 'form-control' : 'form-control-plaintext' " id="bio" v-model="userData.bio" value=""></textarea>
                    -->
                  </div>
                  <div role="group" class="input-group mb-3">
                    <label for="telefono" class="col-sm-6 col-form-label">Telefono</label>
                    <input
                      type="text"
                      :readonly="!edit"
                      :class="edit? 'form-control' : 'form-control-plaintext' "
                      id="telefono"
                      v-model="userData.telefono"
                      value
                    />
                  </div>
                  <div role="group" class="input-group mb-3">
                    <label for="institucion" class="col-sm-6 col-form-label">Institución</label>
                    <input
                      type="text"
                      :readonly="!edit"
                      :class="edit? 'form-control' : 'form-control-plaintext' "
                      id="institucion"
                      v-model="userData.institucion"
                      value
                    />
                  </div>
                  <div role="group" class="input-group mb-3">
                    <label for="pais" class="col-sm-6 col-form-label">Pais</label>
                    <input
                      type="text"
                      :readonly="!edit"
                      :class="edit? 'form-control' : 'form-control-plaintext' "
                      id="pais"
                      v-model="userData.pais"
                      value
                    />
                  </div>
                  <hr />
                  <div class="container">
                    <div class="row">
                      <div class="col-sm-2">
                        <label class="switch switch-primary">
                          <input v-model="edit" type="checkbox" class="switch-input" checked />
                          <span class="switch-slider"></span>
                        </label>
                      </div>
                      <div class="col-sm">¿Editar la información?</div>
                    </div>
                  </div>
                  <button
                    v-if="edit"
                    type="submit"
                    class="btn btn-primary btn-block mt-2"
                  >Guardar cambios</button>
                </form>
              </div>
            </div>
          </div>
          <div class="col-sm-12 col-md-4">
            <div class="card-body bg-dark p-0 clearfix">
              <i class="fas fa-vial bg-primary p-4 px-5 font-4xl mr-3 float-left"></i>
              <nuxt-link class="nav-link" to="/tablero/estudios/listado">
                <div class="h2 text-primary mb-0 pt-3">{{muestreos_count || 0}}</div>
                <div class="text-light text-uppercase font-weight-bold font-xs">Muestreos realizados</div>
              </nuxt-link>
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
export default {
  layout: "tablero",

  data() {
    return {
      ready: false,
      perfil: true,
      edit: false,
      muestreos_count: 0,
      userData: {
        nombre: "",
        apellido: "",
        bio: "",
        telefono: "",
        institucion: "",
        pais: "",
        id: "",
        userId: ""
      }
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
            self.edit = true;
          }
        } else {
          console.log(e);
        }
      });

    let urlcount = `${env.api_host}/usuario/${userId}/estudio/count?access_token=${token}`;
    await this.$axios
      .$get(urlcount)

      .then(function(response) {
        self.muestreos_count = response.count;
      })

      .catch(function(e) {
        if (e.response) {
          let error = e.response.data.error;
          let detalles = error.details;
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
    onSubmit(params) {
      let userData = this.userData;
      let env = require("~/const/env.json");
      let token = localStorage.getItem("token");
      let userId = localStorage.getItem("userId");
      let url = `${env.api_host}/usuario/${userId}/perfil`;
      let self = this;
      let method = "post";

      if (this.perfil) {
        method = "put";
      }

      this.$axios({
        method: method,
        url: url,
        mode: "no-cors",
        headers: {
          "Access-Control-Allow-Origin": "*",
          "Content-Type": "application/json"
        },
        params: {
          access_token: token
        },
        withCredentials: false,
        data: {
          nombre: userData.nombre,
          apellido: userData.apellido,
          bio: userData.bio,
          telefono: userData.telefono,
          institucion: userData.institucion,
          pais: userData.pais
        }
      })

        .then(function(response) {
          self.edit = false;
          self.$toast.success("Perfil actualizado de manera exitosa.", {
            duration: 5000,
            iconPack: "fontawesome",
            icon: "check"
          });
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
