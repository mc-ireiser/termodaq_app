<template>
  <div>
    <noscript>
      <strong>We're sorry but this app doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div class="app flex-row align-items-center">
      <div class="container">
        <div class="row justify-content-center">
          <div class="col-md-8">
            <div class="card-group">
              <div class="card p-4">
                <div class="card-body">
                  <form class>
                    <h1>Login</h1>
                    <p class="text-muted">Inicia sesión en tu cuenta</p>
                    <div role="group" class="input-group mb-3">
                      <div class="input-group-prepend">
                        <div class="input-group-text">
                          <i class="icon-user"></i>
                        </div>
                      </div>
                      <input
                        type="text"
                        placeholder="Usuario"
                        autocomplete="usuario email"
                        class="form-control form-control"
                        id="__BVID__295"
                      />
                    </div>
                    <div role="group" class="input-group mb-4">
                      <div class="input-group-prepend">
                        <div class="input-group-text">
                          <i class="icon-lock"></i>
                        </div>
                      </div>
                      <input
                        type="password"
                        placeholder="Contraseña"
                        autocomplete="current-password"
                        class="form-control form-control"
                        id="__BVID__296"
                      />
                    </div>
                    <div class="row">
                      <div class="col-12">
                        <button type="button" class="btn px-4 mb-2 btn-primary w-100">Entrar</button>
                      </div>
                    </div>
                    <div class="row">
                      <div class="text-center col-6 col-lg-12 col-xl-12">
                        <button type="button" class="btn px-0 btn-link">¿Has olvidado tu contraseña?</button>
                      </div>
                      <div class="text-center d-lg-none d-xl-none">
                        <button type="button" class="btn px-0 btn-link">¡Regístrate ahora!</button>
                      </div>
                    </div>
                  </form>
                </div>
              </div>
              <div class="card text-white bg-primary py-5 d-md-down-none" style="width: 44%;">
                <div class="card-body text-center">
                  <div>
                    <h2>¿Usuario Nuevo?</h2>
                    <p
                      class="mt-4"
                    >Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.</p>
                    <button type="button" class="btn active mt-3 btn-primary">¡Regístrate ahora!</button>
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
export default {};
</script>

<style scoped>
</style>
