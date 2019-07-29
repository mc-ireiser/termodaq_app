<template>
  <div v-if="ready">
    <nav aria-label="breadcrumb">
      <ol class="breadcrumb">
        <li class="breadcrumb-item active" aria-current="page">Nuevo estudio</li>
      </ol>
    </nav>
    <div class="app flex-row">
      <div class="container">
        <div class="row">
          <div class="col-sm-12">
            <div class="card mx-4 p-2">
              <h5>Cargar estudio desde DataFile</h5>
              <form @submit.prevent="loadFileAsText()" class="mt-4">
                <label for="fileToLoad">Archivo</label>
                <div class="form-group mb-3">
                  <input id="fileToLoad" type="file" class="custom-form-file" required />
                </div>
                <div class="form-group">
                  <label for="titulo">Titulo</label>
                  <input
                    v-model="ficha.titulo"
                    type="text"
                    class="form-control"
                    id="titulo"
                    required
                  />
                </div>
                <div class="form-group">
                  <label for="lugar">Lugar</label>
                  <input v-model="ficha.lugar" type="text" class="form-control" id="lugar" required />
                </div>
                <div class="form-group">
                  <label for="descripcion">Descripcion</label>
                  <textarea
                    v-model="ficha.descripcion"
                    class="form-control"
                    id="descripcion"
                    rows="1"
                  ></textarea>
                </div>
                <button class="btn btn-primary" type="submit">Cargar estudio</button>
              </form>
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
import CSV from "comma-separated-values";

export default {
  layout: "tablero",

  data() {
    return {
      ready: false,
      rawData: [],
      ficha: {}
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
    loadFileAsText() {
      let self = this;
      var fileToLoad = document.getElementById("fileToLoad").files[0];
      var fileReader = new FileReader();

      if (!fileToLoad) {
        return;
      }

      if (!this.ficha.titulo & !this.ficha.lugar) {
        return;
      }

      fileReader.onload = async function(fileLoadedEvent) {
        var textFromFileLoaded = await fileLoadedEvent.target.result;
        let studio = await new CSV(textFromFileLoaded, {
          header: [
            "latitude",
            "longitude",
            "date",
            "time",
            "tempInternal",
            "tempWater",
            "tempAir",
            "pressure",
            "uv"
          ]
        }).parse();

        self.rawData = studio;
        self.cargar();
      };

      fileReader.readAsText(fileToLoad, "UTF-8");
    },

    async cargar() {
      let env = require("~/const/env.json");
      let token = localStorage.getItem("token");
      let userId = localStorage.getItem("userId");
      let urlEstudio = `${env.api_host}/usuario/${userId}/estudio?access_token=${token}`;
      let urlFicha = "";
      let estudio = this.rawData;
      let estudioID = "";
      let self = this;

      await this.$axios

        .$post(urlEstudio, {
          data: estudio
        })

        .then(function(response) {
          estudioID = response.id;
          urlFicha = `${env.api_host}/estudio/${response.id}/ficha?access_token=${token}`;
          self.$toast.success("Estudio cargado de manera correcta.", {
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

            self.ficha.titulo = "";
            self.ficha.lugar = "";
            self.ficha.descripcion = "";

            self.$toast.error("Error cargando estudio.", {
              duration: 3500,
              iconPack: "fontawesome",
              icon: "check"
            });
          } else {
            console.log(e);
          }
        });

      await this.$axios

        .$post(urlFicha, self.ficha)

        .then(function(response) {
          self.$router.push("/tablero/estudios/" + estudioID);
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

            self.ficha.titulo = "";
            self.ficha.lugar = "";
            self.ficha.descripcion = "";

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
  }
};
</script>
