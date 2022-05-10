<template>
  <div class="px-2 mx-3 mt-3">
    <!-- HEADER-->
    <div class="pt-4 pb-2 px-3 mb-3 card">
      <div class="pt-0">
        <h3>Configurador de Reportes</h3>
        <b-btn variant="primary" class="mb-3" @click="nueva"
          ><b-icon icon="plus"></b-icon>Nuevo Reporte</b-btn
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
        :busy="modalVisible"
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
      v-model="modalReporte"
      :title="reporte.IDReporte ? 'Editando Reporte' : 'Nuevo Reporte'"
      hide-footer="true"
      size="lg"
      scrollable
      hide-header-close="true"
    >
      <form ref="form" class="container">
        <div class="row justify-content-between">
          <div class="col-6">
            <b-form-group
              id="fieldset-tituloreporte"
              label="Titulo"
              label-for="input-tituloreporte"
            >
              <b-form-input
                id="input-tituloticket"
                v-model="reporte.titulo"
                trim
              ></b-form-input>
            </b-form-group>
            <b-form-group
              id="fieldset-descripcionreporte"
              label="Descripcion"
              label-for="input-descripcionreporte"
            >
              <b-form-input
                id="input-descripcionreporte"
                v-model="reporte.descripcion"
                trim
              ></b-form-input>
            </b-form-group>
            <b-form-group
              id="fieldset-spreporte"
              label="Stored Procedure"
              label-for="input-spreporte"
            >
              <b-form-input
                id="input-spreporte"
                v-model="reporte.sp"
                trim
              ></b-form-input>
            </b-form-group>
          </div>
        </div>
        <div class="row justify-content-between">
          <div class="col-12">
            <b-form-group label="Filtros">
              <b-form-checkbox-group
                id="checkbox-group-filtros"
                v-model="selected"
                style="column-count: 4"
                stacked
                :options="optionsFiltros"
                name="flavour-1"
              ></b-form-checkbox-group>
            </b-form-group>
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
  name: 'ConfiguradorDeReportesPage',
  layout: 'navFooter',
  async asyncData({ $axios }) {
    const token = localStorage.getItem('token')
    $axios.defaults.headers.common['token-auth'] = token

    const testApis = await $axios.$get('/reporteria/')

    const { list } = testApis

    return { list }
  },
  data() {
    return {
      selected: [],
      optionsFiltros: [],

      fields: [
        {
          key: 'actions',
          label: '',
          class: 'actionsStyle',
        },
        {
          key: 'Titulo',
          label: 'Titulo',
          sortable: true,
          class: 'fontSizeSM',
        },
        {
          key: 'Descripcion',
          label: 'Descripcion',
          sortable: false,
          class: 'fontSizeSM',
        },
        {
          key: 'StoredProcedure',
          label: 'Stored Procedure',
          sortable: false,
          class: 'fontSizeSM',
        },
      ],

      modalReporte: false,
      reporte: {},
      ticketModal: {},
      tagPlaceHolder: 'Seleccione una opción',
      files: [],
      filesShow: {},
    }
  },
  async mounted() {
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

    const filtros = await this.$axios.$get('/filtros/')
    const listaFiltros = filtros.list
    listaFiltros.forEach((element) => {
      this.optionsFiltros.push({
        value: element.IDFiltro,
        text: element.Descripcion,
      })
    })
  },

  methods: {
    uploadFiles(files) {
      const formData = new FormData()

      files.forEach((file) => {
        formData.append('multi-files', file)
      })
      this.$axios.$post('/files/', formData, {
        headers: {
          'Content-Type': 'multipart/form-data',
          id: +this.ticket.ticket,
        },
      })
    },

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
      const { list } = await this.$axios.$get('/reporteria/')

      this.list = list
    },

    nueva() {
      this.selected = []
      this.reporte = {
        titulo: '',
        descripcion: '',
        sp: '',
      }
      this.modalReporte = true
    },

    async modificar({ item }) {
      this.reporte = {
        IDReporte: item.IDReporte,
        titulo: item.Titulo,
        descripcion: item.Descripcion,
        sp: item.StoredProcedure,
      }
      this.selected = []
      const filtrosReporte = await this.$axios.$get(
        '/filtros/' + item.IDReporte
      )

      filtrosReporte.item.forEach((element) => {
        this.selected.push(element.IDFiltro)
      })

      this.modalReporte = true
    },

    async updateTableFiles() {
      const objeto = await this.$axios.$get('/files/', {
        headers: {
          id: this.ticket.ticket,
        },
      })
      this.filesShow = objeto.files
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
    async eliminar({ item }) {
      try {
        await this.$axios.$delete('/reporteria/' + item.IDReporte)
        this.$swal.fire('Eliminado', 'Registro Eliminado', 'success')
        this.update()
      } catch (error) {
        this.$swal.fire('Error', 'No se puede eliminar este registro', 'error')
      }
    },

    async guardar() {
      if (this.validar(this.reporte)) {
        const respuesta = await this.$axios.$post('/reporteria/', this.reporte)

        this.reporte.IDReporte = respuesta.item
          ? respuesta.item[0].IDNuevoReporte
          : this.reporte.IDReporte

        const reporteFiltro = {
          IDReporte: this.reporte.IDReporte,
          IDFiltros: this.selected.toString(),
        }

        await this.$axios.$post('/filtros/', reporteFiltro)

        this.update()
        this.modalReporte = false
      }
    },
    cancelar() {
      this.modalReporte = false
      this.selected = []
      this.update()
    },

    validar(objet) {
      const faltantes = []
      const nulleable = ['descripcion']
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
table.b-table[aria-busy='true'] {
  opacity: 0.5;
  background-color: #f2f4f8;
}
</style>
