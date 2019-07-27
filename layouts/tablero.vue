<template>
  <div class="app header-fixed sidebar-lg-show sidebar-fixed">
    <header class="app-header navbar">
      <!-- Header content here -->
      <nav class="navbar">
        <a class="d-flex" href="#">
          <img src="/img/png/termodaq_logo.png" alt="Logo termoDaQ" height="20">
        </a>
        <button class="navbar-toggler sidebar-toggler d-lg-none d-xl-none" type="button" data-toggle="sidebar-show">
          <span class="navbar-toggler-icon"></span>
        </button>
      </nav>
    </header>
    <div class="app-body">
      <div class="sidebar">
        <!-- Sidebar content here -->
        <nav class="sidebar-nav" style="overflow-y: auto;">
          <ul class="nav">
            <li class="nav-title">Estudios</li>
            <li class="nav-item">
              <nuxt-link class="nav-link" to="/tablero/estudios/nuevo">
                <i class="fas fa-file-alt"></i> Nuevo
              </nuxt-link>
            </li>
            <li class="nav-item">
              <nuxt-link class="nav-link" to="/tablero/estudios/listado">
                <i class="fas fa-list-alt"></i> Listado
              </nuxt-link>
            </li>
            <li class="nav-title">Colaboraciones</li>
            <li class="nav-item">
              <nuxt-link class="nav-link" to="/tablero/colaboraciones/listado">
                <i class="fas fa-file-alt"></i> Listado
              </nuxt-link>
            </li>
            <li class="nav-title">Usuario</li>
            <li class="nav-item">
              <nuxt-link class="nav-link" to="/tablero/usuario/perfil">
                <i class="fas fa-user"></i> Perfil
              </nuxt-link>
            </li>
            <li class="nav-item nav-dropdown">
              <a class="nav-link nav-dropdown-toggle" href="#">
                <i class="fas fa-user-shield"></i> Seguridad
              </a>
              <ul class="nav-dropdown-items">
                <li class="nav-title">Contraseña</li>
                <li class="nav-item">
                  <nuxt-link class="nav-link" to="/auth/cambiar-contraseña">
                    <i class="fas fa-exchange-alt"></i> Cambiar
                  </nuxt-link>
                </li>
              </ul>
            </li>
            <li class="nav-item" @click="logout">
              <nuxt-link class="nav-link" to="#">
                <i class="fas fa-sign-out-alt"></i> Salir
              </nuxt-link>
            </li>
            <li class="nav-item nav-dropdown mt-auto">
              <a class="nav-link nav-dropdown-toggle nav-link-primary" href="#">
                <i class="fab fa-github-alt"></i> Github
              </a>
              <ul class="nav-dropdown-items">
                <li class="nav-item">
                  <a class="nav-link" href="https://github.com/mc-ireiser/termoDaQ" target="_blank">
                    <i class="fas fa-code-branch"></i> termoDaQ
                  </a>
                </li>
                <li class="nav-item">
                  <a class="nav-link" href="https://github.com/mc-ireiser/termodaq_firmware_atmega328p" target="_blank">
                    <i class="fas fa-code-branch"></i> Firmware ATmega328p
                  </a>
                </li>
                <li class="nav-item">
                  <a class="nav-link" href="https://github.com/mc-ireiser/termodaq_firmware_attiny85" target="_blank">
                    <i class="fas fa-code-branch"></i> Firmware ATtiny85
                  </a>
                </li>
                <li class="nav-item">
                  <a class="nav-link" href="https://github.com/mc-ireiser/termodaq_link" target="_blank">
                    <i class="fas fa-code-branch"></i> LINK
                  </a>
                </li>
                <li class="nav-item">
                  <a class="nav-link" href="https://github.com/mc-ireiser/termodaq_api" target="_blank">
                    <i class="fas fa-code-branch"></i> API
                  </a>
                </li>
                <li class="nav-item">
                  <a class="nav-link" href="https://github.com/mc-ireiser/termodaq_app" target="_blank">
                    <i class="fas fa-code-branch"></i> APP
                  </a>
                </li>
              </ul>
            </li>
          </ul>
        </nav>
      </div>
      <main class="main">
        <!-- Main content here -->
        <div class="container-fluid px-0 mt-0">
          <nuxt/>
        </div>
      </main>
    </div>
    <footer class="app-footer">
      <!-- Footer content here -->
    </footer>
  </div>
</template>

<script>
export default {

  mounted() {
    let token = localStorage.getItem("token")
    if (!token) {
      this.$router.push('/auth/login')
    }
  },

  methods: {
    logout(params) {
      let env = require('~/const/env.json');
      let url = env.api_host + '/usuario/logout'
      let token = localStorage.getItem("token")
      let self = this

      this.$axios({
        method: 'post',
        url: url,
        mode: 'no-cors',
        headers: {
          'Access-Control-Allow-Origin': '*',
          'Content-Type': 'application/json',
        },
        params: {
          access_token: token
        },
        withCredentials: false
      })

      .then(function (response) {
        localStorage.setItem("token", '')
        localStorage.setItem("userId", '')
        self.$router.push('/auth/login')
      })

      .catch(function (e) {
        if (e.response) {
          let error = e.response.data.error
          let detalles = error.details
          console.log(error.statusCode)

          localStorage.setItem("token", '')
          localStorage.setItem("userId", '')
          self.$router.push('/auth/login')

          self.$toast.error('Error, en validación', {
            duration: 10000,
            iconPack: 'fontawesome',
            icon : 'times'
          })

        } else {
          console.log(e)
        }
      })
    }
  }
};
</script>


<style>
</style>
