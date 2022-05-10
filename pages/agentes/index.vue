<template>
  <div class="px-2 mx-3 mt-3">
    <!-- HEADER-->
    <div class="pt-4 pb-2 px-3 mb-3 card">
      <div class="pt-0">
        <h3>Agentes de Soporte</h3>
        <b-btn variant="primary" class="mb-3" @click="nueva"
          ><b-icon icon="plus"></b-icon> Alta de Agente</b-btn
        >
      </div>
    </div>

    <div class="body card">
      <b-table
        hover
        striped
        sticky-header="40vh"
        :items="list"
        :fields="fields"
        small
        class="mt-0"
      >
        <template #cell(actions)="row">
          <b-dropdown
            dropright
            toggle-class="text-decoration-none"
            no-caret
            size="sm"
          >
            <template #button-content>
              <b-icon icon="list"></b-icon>
            </template>

            <b-dropdown-item @click="modificar(row)">
              <b-icon icon="blockquote-right"></b-icon> Modificar
            </b-dropdown-item>
            <b-dropdown-item @click="confirmarEliminar(row)">
              <b-icon icon="trash"></b-icon> Eliminar
            </b-dropdown-item>
          </b-dropdown>
        </template>
      </b-table>
    </div>

    <b-modal
      v-model="modalVisible"
      :title="agente.IDAgente ? 'Editando Agente' : 'Alta Nuevo Agente'"
      hide-footer="true"
      size="xl"
      scrollable
      hide-header-close="true"
    >
      <form ref="form" class="container">
        <div class="row justify-content-between">
          <div class="col-4">
            <b-form-group
              id="fieldset-nombreagente"
              label="Nombre"
              label-for="input-nombreagente"
            >
              <b-form-input
                id="input-nombreagente"
                v-model="agente.nombre"
                trim
              ></b-form-input>
            </b-form-group>
            <b-form-group
              id="fieldset-correoagente"
              label="Correo"
              label-for="input-correoagente"
            >
              <b-form-input
                id="input-correoagente"
                v-model="agente.correo"
                trim
                type="email"
              ></b-form-input>
            </b-form-group>
            <b-form-group
              id="fieldset-telefonogente"
              label="Telefono"
              label-for="input-telefonoagente"
            >
              <b-form-input
                id="input-telefonoagente"
                v-model="agente.telefono"
                trim
                type="tel"
              ></b-form-input>
            </b-form-group>
            <b-form-group
              id="fieldset-passwordgente"
              label="Password"
              label-for="input-passwordagente"
            >
              <b-form-input
                id="input-passwordagente"
                v-model="agente.password"
                v-b-tooltip.hover.bottomright="
                  'Al modificar este campo la contraseña actual cambiara'
                "
                :placeholder="agente.IDAgente ? 'Contraseña Asignada' : ''"
                type="password"
                trim
              ></b-form-input>
            </b-form-group>
          </div>
          <div class="col-4">
            <b-form-group
              id="fieldset-perfilagente"
              label="Perfil"
              label-for="input-perfilagente"
            >
              <v-select
                v-model="agente.perfil"
                label="text"
                :placeholder="tagPlaceHolder"
                :options="options.optionsPerfiles"
                :reduce="(a) => a.value"
                class="sizeBox"
              />
            </b-form-group>
            <b-form-group
              id="fieldset-oficinaagente"
              label="Oficina"
              label-for="input-oficinaagente"
            >
              <v-select
                v-model="agente.oficina"
                label="text"
                :placeholder="tagPlaceHolder"
                :options="options.optionsOficinas"
                :reduce="(a) => a.value"
                class="sizeBox"
              />
            </b-form-group>
            <b-form-group
              id="fieldset-especialidadagente"
              label="Especialidad"
              label-for="input-especialidadagente"
            >
              <v-select
                v-model="agente.especialidad"
                label="text"
                :placeholder="tagPlaceHolder"
                :options="options.optionsEspecialidades"
                :reduce="(a) => a.value"
                class="sizeBox"
              />
            </b-form-group>
            <div class="row ustify-content-between">
              <div class="col-2">
                <b-form-group
                  id="fieldset-statusagente"
                  label="Activo"
                  label-for="input-statusagente"
                >
                  <b-form-checkbox
                    id="checkbox-1"
                    v-model="agente.Activo"
                    name="checkbox-1"
                    :value="parseInt('1', 10)"
                    :unchecked-value="parseInt('0', 10)"
                  >
                  </b-form-checkbox>
                </b-form-group>
              </div>
              <div class="col-2">
                <b-form-group
                  id="fieldset-notiagente"
                  label="Notificaciones"
                  label-for="input-notiagente"
                >
                  <b-form-checkbox
                    id="checkbox-2"
                    v-model="agente.Notificacion"
                    name="checkbox-2"
                    :value="parseInt('1', 10)"
                    :unchecked-value="parseInt('0', 10)"
                  >
                  </b-form-checkbox>
                </b-form-group>
              </div>
            </div>
          </div>
        </div>
      </form>

      <footer class="modal-footer">
        <b-button class="mt-2" variant="primary" @click="guardar"
          >Guardar</b-button
        >
        <b-button class="mt-2" variant="danger" @click="cancelar"
          >Cancelar</b-button
        >
      </footer>
    </b-modal>
  </div>
</template>

<script>
import Vue from 'vue'
import { BootstrapVue, BootstrapVueIcons } from 'bootstrap-vue'
import VueSweetalert2 from 'vue-sweetalert2'
import 'sweetalert2/dist/sweetalert2.min.css'
import vSelect from 'vue-select'
import 'vue-select/dist/vue-select.css'

Vue.component('VSelect', vSelect)

Vue.use(BootstrapVue)
Vue.use(BootstrapVueIcons)
Vue.use(VueSweetalert2)

export default {
  name: 'AgentesPage',
  layout: 'navFooter',
  async asyncData({ $axios }) {
    const token = localStorage.getItem('token')
    $axios.defaults.headers.common['token-auth'] = token

    const testApis = await $axios.$get('/agentes/')

    const { list } = testApis
    // delete $axios.defaults.headers.common['token-auth']
    return { list }
  },
  data() {
    return {
      selected: {
        selectedPerfiles: [],
        selectedOficinas: [],
        selectedEspecialidades: [],
      },

      options: {
        optionsPerfiles: [],
        optionsOficinas: [],
        optionsEspecialidades: [],
      },

      fields: [
        {
          key: 'actions',
          label: '',
          class: 'actionsStyle',
        },
        {
          key: 'Nombre',
          label: 'Nombre',
          sortable: true,
          class: 'fontSizeSM',
        },
        {
          key: 'Correo',
          label: 'Correo',
          sortable: false,
          class: 'fontSizeSM',
        },
        {
          key: 'Telefono',
          label: 'Telefono',
          sortable: false,
          class: 'fontSizeSM',
        },
        {
          key: 'PERFIL',
          label: 'Perfil',
          sortable: false,
          class: 'fontSizeSM',
        },
        {
          key: 'OFICINA',
          label: 'Oficina',
          sortable: false,
          class: 'fontSizeSM',
        },
        {
          key: 'ESPECIALIDAD',
          label: 'Especialidad',
          sortable: false,
          class: 'fontSizeSM',
        },
        {
          key: 'TEXTOACTIVO',
          label: '¿Activo?',
          sortable: false,
          class: 'fontSizeSM',
        },
        {
          key: 'TEXTONOTIFICACION',
          label: '¿Recibe Notificación?',
          sortable: false,
          class: 'fontSizeSM',
        },
      ],

      modalVisible: false,
      agente: {},
      tagPlaceHolder: 'Seleccione una opción',
    }
  },

  async mounted() {
    // const { list } = await this.$axios.$get('/clientes/')
    const perfiles = await this.$axios.$get('/perfiles/')
    const oficina = await this.$axios.$get('/oficinas/')
    const especialidad = await this.$axios.$get('/especialidades/')
    const listaPerfiles = perfiles.list
    const listaOficinas = oficina.list
    const listaEspecialidades = especialidad.list
    const perfil = localStorage.getItem('perfil')

    if (perfil !== '1') {
      this.$swal
        .fire({
          type: 'error',
          title: 'Oops...',
          text: 'No tienes permisos para acceder a esta página',
          confirmButtonText: 'OK',
        })
        .then(() => {
          this.$router.push('/')
        })
    }

    listaPerfiles.forEach((element) => {
      this.options.optionsPerfiles.push({
        value: element.IDPerfil,
        text: element.Descripcion,
      })
    })
    listaOficinas.forEach((element) => {
      this.options.optionsOficinas.push({
        value: element.IDOficina,
        text: element.Descripcion,
      })
    })
    listaEspecialidades.forEach((element) => {
      this.options.optionsEspecialidades.push({
        value: element.IDEspecialidad,
        text: element.Descripcion,
      })
    })
  },
  methods: {
    isNumber(evt) {
      evt = evt || window.event
      const charCode = evt.which ? evt.which : evt.keyCode
      if (charCode > 31 && (charCode < 48 || charCode > 57)) {
        evt.preventDefault()
      } else {
        return true
      }
    },

    isNumberDate(evt, model) {
      if (this.selected[model]?.length === 10) {
        evt.preventDefault()
      }
      evt = evt || window.event
      const charCode = evt.which ? evt.which : evt.keyCode
      if (
        charCode > 31 &&
        (charCode < 48 || charCode > 57) &&
        charCode !== 45
      ) {
        evt.preventDefault()
      } else {
        if (
          this.selected[model]?.length === 4 ||
          this.selected[model]?.length === 7
        ) {
          this.selected[model] += '-'
        }
        return true
      }
    },

    isNumberDateTicket(evt, model) {
      if (this.ticket[model]?.length === 10) {
        evt.preventDefault()
      }
      evt = evt || window.event
      const charCode = evt.which ? evt.which : evt.keyCode
      if (
        charCode > 31 &&
        (charCode < 48 || charCode > 57) &&
        charCode !== 45
      ) {
        evt.preventDefault()
      } else {
        if (
          this.ticket[model]?.length === 4 ||
          this.ticket[model]?.length === 7
        ) {
          this.ticket[model] += '-'
        }
        return true
      }
    },

    async update() {
      const { list } = await this.$axios.$get('/agentes/')
      this.list = list
    },

    nueva() {
      this.agente = {
        nombre: '',
        perfil: '',
        correo: '',
        telefono: '',
        oficina: '',
        especialidad: '',
        password: '',
      }
      this.modalVisible = true
    },
    validarCorreo() {
      const correo = this.agente.correo
      const regex = /^[-\w.%+]{1,64}@(?:[A-Z0-9-]{1,63}\.){1,125}[A-Z]{2,63}$/i

      if (regex.test(correo)) {
        return true
      } else {
        this.$swal.fire('Error', 'Correo No Valido', 'error')
        return false
      }
    },

    modificar({ item }) {
      this.agente = {
        IDAgente: item.IDAgente,
        nombre: item.Nombre,
        perfil: item.IDPerfil,
        correo: item.Correo,
        telefono: item.Telefono,
        oficina: item.IDOficina,
        especialidad: item.IDEspecialidad,
        Activo: item.Activo,
        Notificacion: item.Notificacion,
      }
      console.log(this.agente)

      this.modalVisible = true
    },

    async eliminar({ item }) {
      try {
        await this.$axios.$delete('/agentes/' + item.IDAgente)
        this.$swal.fire('Eliminado', 'Agente Eliminado', 'success')
        this.update()
      } catch (error) {
        this.$swal.fire('Error', 'No se puede eliminar este usuario', 'error')
        return false
      }
    },

    async guardar() {
      try {
        if (this.validarCorreo() && this.validar(this.agente)) {
          await this.$axios.$post('/agentes/', this.agente)
          console.log('agente', this.agente)
          this.modalVisible = false
          this.update()
        }
      } catch (error) {
        this.$swal.fire('Error', 'No se puede guardar este usuario', 'error')
      }
    },
    cancelar() {
      this.modalVisible = false

      this.update()
    },
    cancelarNuevo() {
      this.modalNuevo = false
    },
    async guardarNuevo() {
      this.update()
      console.log('soy ticket viajero')
      console.log(this.ticket)
      if (this.validar(this.ticket)) {
        const resp = await this.$axios.$post('/solicitudes/', this.ticket)
        const IDTicketNuevo = resp.item[0].IDTicketNuevo
        this.ticket.ticket = IDTicketNuevo
        this.uploadFiles(this.files)
        this.modalNuevo = false
        this.update()
      }
    },

    confirmarEliminar(row) {
      this.$swal
        .fire({
          title: '¿Deseas eliminar este registro?',
          text: 'Esta acción no se puede revertir',
          icon: 'warning',
          showCancelButton: true,
          confirmButtonColor: '#3085d6',
          cancelButtonColor: '#d33',
          confirmButtonText: 'Eliminar',
        })
        .then((result) => {
          if (result.isConfirmed) {
            this.eliminar(row)
          }
        })
    },

    fecha() {
      const date = new Date()
      const fecha =
        date.getFullYear() +
        '-' +
        String(date.getMonth() + 1).padStart(2, '0') +
        '-' +
        String(date.getDate()).padStart(2, '0')

      return fecha
    },
    hora() {
      const date = new Date()
      const hora =
        date.getHours() + ':' + date.getMinutes() + ':' + date.getSeconds()
      return hora
    },
    validar(objet) {
      const faltantes = []
      const nulleable = ['telefono', 'Activo', 'Validar']
      Object.keys(objet).forEach((key) => {
        if (objet[key] === '' || objet[key] === null) {
          if (!nulleable.includes(key)) {
            faltantes.push(key)
          }
        }
      })
      if (faltantes.length) {
        this.$swal.fire(
          'Faltan datos',
          'Faltan datos en el formulario ' + faltantes.toString(),
          'warning'
        )
        return false
      } else {
        return true
      }
    },
  },
}
</script>

<style>
.actionsStyle {
  width: 5px;
}
body {
  min-height: 850px;
  max-height: 100%;
  top: 0;
  margin: 0;
  padding: 0;
  background-color: #f2f4f8;
}

.footerPX,
section#main,
body {
  width: 100%;
  position: absolute;
}

section#main {
  min-height: 100%;
}

.footerPX {
  bottom: 0;
}

.contenedor {
  display: flex;
  flex-direction: row;
  height: 100vh;
}
.cuerpo {
  flex-grow: 1;
}
.contenderoFiltros {
  display: flex;
  flex-wrap: wrap;
  /* justify-content: space-between; */
}
.filtro {
  margin-top: 5px;
  margin-right: 15px;
  width: 240px;
  /* max-height: 50px; */
}

.fontSizeSM {
  font-size: 12px;
}
.fz-g {
  font-size: 12px;
}
.agenteBG {
  font-weight: bold;
}
.divisor {
  margin-right: 10px;
  margin-left: 10px;
}
</style>
