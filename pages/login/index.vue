<template>
  <!-- CONTENEDOR PRINCIPAL -->

  <div class="row mb-0 login">
    <!-- FORM LOGIN -->
    <div
      class="text-center mt-5 mx-5 col"
      cz-shortcut-listen="true"
      @keyup.enter="validacion"
    >
      <div class="form-signin mx-5">
        <!-- <img class="mb-4" src="@/src/images/logo.jpg" alt="" width="150" height="150"> -->

        <!-- LOGO ADAGIO -->
        <img
          src="@/src/images/Logo_Adagio_fondo_azul.png"
          class="rounded-circle sz mb-4"
          alt=""
        />
        <h1 class="">¡Hola bienvenido!</h1>
        <p><em>Inicia Sesión para comenzar</em></p>

        <div class="mx-5">
          <!-- <label for="inputEmail" class="sr-only">Email address</label> -->
          <input
            id="input-Correo"
            v-model="datos.Correo"
            type="email"
            class="form-control mx-2"
            placeholder="Correo electrónico"
            required=""
            autofocus=""
          />
          <!-- <label for="inputPassword" class="sr-only">Password</label> -->
          <input
            id="input-Password"
            v-model="datos.Password"
            type="password"
            class="form-control mt-2 mx-2"
            placeholder="Contraseña"
            required=""
          />
          <div class="checkbox mb-3 mt-3"></div>
        </div>
        <div class="row">
          <div class="col"></div>
          <button
            class="btn btn-lg btn-primary btn-block col"
            type="submit"
            @click="validacion"
          >
            Acceder
          </button>

          <div class="col"></div>
        </div>

        <p class="mt-5 mb-3 text-muted">© 2022</p>
      </div>
    </div>
    <!-- IMAGEN LADO IZQUIERDO -->
    <div class="col">
      <img
        src="@/src/images/loginBackground.jpg"
        class="img-fluid accounts-image__overlay"
        alt="Responsive image"
      />
      <!-- <px-carousel class="accounts-image__overlay"/> -->
    </div>
  </div>
</template>

<script>
// import PxCarousel from "@/components/PxCarousel.vue"
import Vue from 'vue'
import { ToastPlugin } from 'bootstrap-vue'
Vue.use(ToastPlugin)

export default {
  name: 'PxLogin',
  layout: 'LoginLayout',
  //   components: { PxCarousel }
  data() {
    return {
      datos: {},
    }
  },
  created() {
    localStorage.removeItem('token')
    localStorage.removeItem('nombre')
    localStorage.removeItem('perfil')
    delete this.$axios.defaults.headers.common['token-auth']
  },

  methods: {
    async validacion() {
      try {
        const item = await this.$axios.$post('/login', this.datos)
        localStorage.setItem('token', item.token)
        localStorage.setItem('nombre', item.item.Nombre)
        localStorage.setItem('perfil', item.item.IDPerfil)
        localStorage.setItem('IDAgente', item.item.IDAgente)
        localStorage.setItem('Correo', item.item.Correo)

        this.$router.push({ path: `/solicitudes` })
      } catch (error) {
        this.toast(
          'b-toaster-bottom-full',
          true,
          'Datos de acceso incorrectos o el usuario no se encuentra vigente'
        )
      }
    },
    toast(toaster, append = false, menssage) {
      this.counter++
      this.$bvToast.toast(menssage, {
        title: `ERROR`,
        toaster,
        solid: true,
        variant: 'danger',
        appendToast: append,
      })
    },
  },
}
</script>

<style scoped>
body {
  max-height: 100vh !important;
  min-height: 720px !important;
  top: 0;
  margin: 0;
  padding: 0;
  background-color: #f2f4f8;
  height: 100%;
}
.accounts-image__overlay {
  position: absolute;
  top: 0;

  height: 100%;

  display: grid;
  grid-template-rows: 1fr 1fr;
}
.sz {
  height: 10rem;
  width: 10rem;
}
.login {
  height: 100vh !important;
  width: 100vw !important;
  min-height: 720px !important;
}
html,
body {
  height: 100vh !important;
  width: 100vw !important;
  min-height: 720px !important;
}
</style>
