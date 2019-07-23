<template>
  <div>
    <noscript>
      <strong>Lo sentimos, pero esta aplicación no funciona correctamente sin JavaScript habilitado. Por favor, activelo para continuar.</strong>
    </noscript>
    <div class="app flex-row align-items-center">
      <div class="container">
        <div class="row justify-content-center">
          <div class="col-sm-8 col-md-6">
            <div class="card mx-4">
              <div class="card-body p-4">
                <form class>
                  <h1>Registro</h1>
                  <p class="text-muted">Complete todos los campos para crea su cuenta</p>
                  <div role="group" class="input-group mb-3">
                    <div class="input-group-prepend">
                      <div class="input-group-text">
                        <i class="icon-user"></i>
                      </div>
                    </div>
                    <input
                      type="text"
                      placeholder="Usuario"
                      autocomplete="usuario"
                      class="form-control form-control"
                      id="__BVID__295"
                    />
                  </div>
                  <div role="group" class="input-group mb-3">
                    <div class="input-group-prepend">
                      <div class="input-group-text">@</div>
                    </div>
                    <input
                      type="text"
                      placeholder="Email"
                      autocomplete="email"
                      class="form-control form-control"
                      id="__BVID__296"
                    />
                  </div>
                  <div role="group" class="input-group mb-3">
                    <div class="input-group-prepend">
                      <div class="input-group-text">
                        <i class="icon-lock"></i>
                      </div>
                    </div>
                    <input
                      type="password"
                      placeholder="Contraseña"
                      autocomplete="new-password"
                      class="form-control form-control"
                      id="__BVID__297"
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
                      placeholder="Repita su contraseña"
                      autocomplete="new-password"
                      class="form-control form-control"
                      id="__BVID__298"
                    />
                  </div>
                  <button type="button" class="btn btn-primary btn-block">Crear una cuenta</button>
                </form>
              </div>
              <div class="card-footer p-4">
                <div class="row">
                  <div class="col-12">
                    <button type="button" class="btn btn btn-secondary btn-block">
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
export default {};
</script>

<style scoped>
</style>
