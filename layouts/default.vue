<template>
  <div class="app header-fixed sidebar-lg-show sidebar-fixed">
    <header class="app-header navbar">
      <!-- Header content here -->
      <nav class="navbar">
        <a class="d-flex" href="#">
          <img src="/img/png/termodaq_logo.png" alt="Logo termoDaQ" height="20">
        </a>
        <button class="navbar-toggler sidebar-toggler" type="button" data-toggle="sidebar-show">
          <span class="navbar-toggler-icon"></span>
        </button>
      </nav>
    </header>
    <div class="app-body">
      <div class="sidebar">
        <!-- Sidebar content here -->
        <nav class="sidebar-nav">
          <ul class="nav">
            <li class="nav-title">Estudios</li>
            <li class="nav-item">
              <a class="nav-link" href="#">
                <i class="fas fa-file-alt"></i> Nuevo
              </a>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="#">
                <i class="fas fa-list-alt"></i> Listado
              </a>
            </li>
            <li><hr class="bg-light w-75"></li>
            <li class="nav-title">Colaboraciones</li>
            <li class="nav-item">
              <a class="nav-link" href="#">
                <i class="fas fa-file-alt"></i> Listado
              </a>
            </li>
            <li><hr class="bg-light w-75"></li>
            <li class="nav-title">Usuario</li>
            <li class="nav-item">
              <a class="nav-link" href="#">
                <i class="fas fa-user"></i> Perfil
              </a>
            </li>
            <li class="nav-item nav-dropdown">
              <a class="nav-link nav-dropdown-toggle" href="#">
                <i class="fas fa-user-shield"></i> Seguridad
              </a>
              <ul class="nav-dropdown-items">
                <li class="nav-title">Contraseña</li>
                <li class="nav-item">
                  <a class="nav-link" href="#">
                    <i class="fas fa-exchange-alt"></i> Cambiar
                  </a>
                </li>
                <li class="nav-item">
                  <a class="nav-link" href="#">
                    <i class="fas fa-redo"></i> Restablecer
                  </a>
                </li>
              </ul>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="#">
                <i class="fas fa-sign-out-alt"></i> Salir
              </a>
            </li>
          </ul>
        </nav>
      </div>
      <main class="main">
        <!-- Main content here -->
      </main>
    </div>
    <footer class="app-footer">
      <!-- Footer content here -->
    </footer>
  </div>
</template>

<script>
export default {};
</script>


<style>
</style>
