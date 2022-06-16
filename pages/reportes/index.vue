<template>
  <div class="px-2 mx-3 mt-3">
    <!-- HEADER-->
    <div class="pt-4 pb-2 px-3 mb-3 card">
      <div class="pt-0">
        <h3>Modulo de Reportes</h3>
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
          <b-button size="sm" @click="seleccionar(row)">
            <b-icon icon="file-earmark-bar-graph"></b-icon>
          </b-button>
        </template>
      </b-table>
    </div>

    <b-modal
      v-model="modalReporte"
      :title="reporte.titulo"
      hide-footer="true"
      size="xl"
      scrollable
      hide-header-close="true"
    >
      <form ref="form" class="container">
        <!-- <div class="row justify-content-between">
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
        </div> -->
        <h5>Filtros</h5>
        <div class="border-bottom mt-3"></div>
        <div class="mt-2 mb-5 contenderoFiltros">
          <div v-if="filtrosShow.indexOf(13) != -1" class="filtro">
            Agente
            <v-select
              v-model="selected.selectedAgente"
              label="text"
              multiple
              :placeholder="tagPlaceHolder"
              :options="options.optionsAgente"
              class="sizeBox"
            />
          </div>
          <div v-if="filtrosShow.indexOf(1) != -1" class="filtro">
            Clientes
            <v-select
              v-model="selected.selectedCliente"
              label="text"
              multiple
              :placeholder="tagPlaceHolder"
              :options="options.optionsClientes"
              class="sizeBox"
            />
          </div>
          <div v-if="filtrosShow.indexOf(10) != -1" class="filtro">
            Status
            <v-select
              v-model="selected.selectedStatus"
              label="text"
              multiple
              :placeholder="tagPlaceHolder"
              :options="options.optionsStatus"
              class="sizeBox"
            />
          </div>
          <div v-if="filtrosShow.indexOf(6) != -1" class="filtro">
            Modulos
            <v-select
              v-model="selected.selectedModulos"
              label="text"
              multiple
              :placeholder="tagPlaceHolder"
              :options="options.optionsModulos"
              class="sizeBox"
            />
          </div>
          <div v-if="filtrosShow.indexOf(2) != -1" class="filtro">
            Clasificación
            <v-select
              v-model="selected.selectedClasificacion"
              label="text"
              multiple
              :placeholder="tagPlaceHolder"
              :options="options.optionsClasificacion"
              class="sizeBox"
            />
          </div>
          <div v-if="filtrosShow.indexOf(3) != -1" class="filtro">
            Dificultades
            <v-select
              v-model="selected.selectedDificultad"
              label="text"
              multiple
              :placeholder="tagPlaceHolder"
              :options="options.optionsDificultad"
              class="sizeBox"
            />
          </div>

          <div v-if="filtrosShow.indexOf(4) != -1" class="filtro">
            Especialidad
            <v-select
              v-model="selected.selectedEspecialidad"
              label="text"
              multiple
              :placeholder="tagPlaceHolder"
              :options="options.optionsEspecialidades"
              class="sizeBox"
            />
          </div>
          <div v-if="filtrosShow.indexOf(5) != -1" class="filtro">
            Errores
            <v-select
              v-model="selected.selectedErrores"
              label="text"
              multiple
              :placeholder="tagPlaceHolder"
              :options="options.optionsErrores"
              class="sizeBox"
            />
          </div>

          <div v-if="filtrosShow.indexOf(7) != -1" class="filtro">
            Oficinas
            <v-select
              v-model="selected.selectedOficinas"
              label="text"
              multiple
              :placeholder="tagPlaceHolder"
              :options="options.optionsOficinas"
              class="sizeBox"
            />
          </div>

          <div v-if="filtrosShow.indexOf(8) != -1" class="filtro">
            Perfiles
            <v-select
              v-model="selected.selectedPerfiles"
              label="text"
              multiple
              :placeholder="tagPlaceHolder"
              :options="options.optionsPerfiles"
              class="sizeBox"
            />
          </div>

          <div v-if="filtrosShow.indexOf(9) != -1" class="filtro">
            Prioridades
            <v-select
              v-model="selected.selectedPrioridad"
              label="text"
              multiple
              :placeholder="tagPlaceHolder"
              :options="options.optionsPrioridad"
              class="sizeBox"
            />
          </div>

          <div v-if="filtrosShow.indexOf(11) != -1" class="filtro">
            Fecha Inicial
            <b-input-group>
              <b-form-input
                id="fechaini-input"
                v-model="selected.selectedFechaIni"
                type="text"
                placeholder="YYYY-MM-DD"
                autocomplete="off"
                class="sizeBox"
                @keypress="isNumberDate($event, 'selectedFechaIni')"
              ></b-form-input>
              <b-input-group-append>
                <b-form-datepicker
                  v-model="selected.selectedFechaIni"
                  button-only
                  right
                  aria-controls="fechaini-input"
                  class="sizeBox"
                  @context="onContext"
                ></b-form-datepicker>
              </b-input-group-append>
            </b-input-group>
          </div>
          <div v-if="filtrosShow.indexOf(12) != -1" class="filtro">
            Fecha Final
            <b-input-group>
              <b-form-input
                id="fechafin-input"
                v-model="selected.selectedFechaFin"
                type="text"
                placeholder="YYYY-MM-DD"
                autocomplete="off"
                class="sizeBox"
                @keypress="isNumberDate($event, 'selectedFechaFin')"
              ></b-form-input>
              <b-input-group-append>
                <b-form-datepicker
                  v-model="selected.selectedFechaFin"
                  button-only
                  right
                  aria-controls="fechafin-input"
                  class="sizeBox"
                  @context="onContext"
                ></b-form-datepicker>
              </b-input-group-append>
            </b-input-group>
          </div>
        </div>
      </form>

      <footer class="modal-footer">
        <b-button class="mt-2" variant="primary" @click="generar"
          >Generar</b-button
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
import { BootstrapVue, BootstrapVueIcons, ToastPlugin } from 'bootstrap-vue'
import VueSweetalert2 from 'vue-sweetalert2'
import 'sweetalert2/dist/sweetalert2.min.css'
import vSelect from 'vue-select'
import 'vue-select/dist/vue-select.css'

Vue.component('VSelect', vSelect)

Vue.use(BootstrapVue)
Vue.use(BootstrapVueIcons)
Vue.use(VueSweetalert2)
Vue.use(ToastPlugin)

export default {
  name: 'ReportesPage',
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
      ],

      modalReporte: false,
      reporte: {},
      ticketModal: {},
      tagPlaceHolder: 'Seleccione una opción',

      selected: {
        selectedCliente: [],
        selectedAgente: [],
        selectedStatus: [],
        selectedModulos: [],
        selectedUsuario: [],
        selectedClasificacion: [],
        selectedEspecialidad: [],
        selectedPrioridad: [],
        selectedDificultad: [],
        selectedErrores: [],
        selectedOficinas: [],
        selectedPerfiles: [],
        selectedFechaIni: null,
        selectedFechaFin: null,
      },
      filtrosShow: [],
      options: {
        optionsClientes: [],
        optionsClasificacion: [],
        optionsDificultad: [],
        optionsEspecialidades: [],
        optionsErrores: [],
        optionsModulos: [],
        optionsOficinas: [],
        optionsPerfiles: [],
        optionsPrioridad: [],
        optionsStatus: [],
        optionsAgente: [],
      },
    }
  },
  async mounted() {
    // const filtros = await this.$axios.$get('/filtros/')
    // const listaFiltros = filtros.list
    // listaFiltros.forEach((element) => {
    //   this.optionsFiltros.push({
    //     value: element.IDFiltro,
    //     text: element.Descripcion,
    //   })
    // })
    const clientes = await this.$axios.$get('/clientes/') /// ok
    const clasificacion = await this.$axios.$get('/clasificaciones/') /// ok
    const dificultad = await this.$axios.$get('/dificultades/') /// ok
    const especialidades = await this.$axios.$get('/especialidades/') /// ok
    const errores = await this.$axios.$get('/errores/') /// ok
    const modulos = await this.$axios.$get('/modulos/') /// ok
    const oficinas = await this.$axios.$get('/oficinas/') /// ok
    const perfiles = await this.$axios.$get('/perfiles/') /// ok
    const prioridades = await this.$axios.$get('/prioridades/') /// ok
    const status = await this.$axios.$get('/status/')
    const agentes = await this.$axios.$get('/agentes/')

    const listaClientes = clientes.list
    const listaClasificacion = clasificacion.list
    const listaDificultades = dificultad.list
    const listaEspecialidades = especialidades.list
    const listaErrores = errores.list
    const listaModulos = modulos.list
    const listaOficinas = oficinas.list
    const listaPerfiles = perfiles.list
    const listaPrioridades = prioridades.list
    const listaStatus = status.list
    const listaAgentes = agentes.list

    listaClientes.forEach((element) => {
      this.options.optionsClientes.push({
        value: element.IDCliente,
        text: element.NombreComercial,
      })
    })
    listaClasificacion.forEach((element) => {
      this.options.optionsClasificacion.push({
        value: element.IDClasificacion,
        text: element.Descripcion,
      })
    })
    listaDificultades.forEach((element) => {
      this.options.optionsDificultad.push({
        value: element.IDDificultad,
        text: element.Descripcion,
      })
    })
    listaEspecialidades.forEach((element) => {
      this.options.optionsEspecialidades.push({
        value: element.IDEspecialidad,
        text: element.Descripcion,
      })
    })
    listaErrores.forEach((element) => {
      this.options.optionsErrores.push({
        value: element.IDTipoError,
        text: element.Descripcion,
      })
    })
    listaModulos.forEach((element) => {
      this.options.optionsModulos.push({
        value: element.IDModulo,
        text: element.Descripcion,
      })
    })
    listaOficinas.forEach((element) => {
      this.options.optionsOficinas.push({
        value: element.IDOficina,
        text: element.Descripcion,
      })
    })
    listaPerfiles.forEach((element) => {
      this.options.optionsPerfiles.push({
        value: element.IDPerfil,
        text: element.Descripcion,
      })
    })
    listaPrioridades.forEach((element) => {
      this.options.optionsPrioridad.push({
        value: element.IDPrioridad,
        text: element.Descripcion,
      })
    })
    listaStatus.forEach((element) => {
      this.options.optionsStatus.push({
        value: element.IDStatus,
        text: element.Descripcion,
      })
    })
    listaAgentes.forEach((element) => {
      this.options.optionsAgente.push({
        value: element.IDAgente,
        text: element.Nombre,
      })
    })
  },

  methods: {
    s2ab(s) {
      // Cadena a secuencia de caracteres
      const buf = new ArrayBuffer(s.length)
      const view = new Uint8Array(buf)
      for (let i = 0; i !== s.length; ++i) view[i] = s.charCodeAt(i) & 0xff
      return buf
    },

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

    limpiarFiltros() {
      this.selected.selectedCliente = []
      this.selected.selectedAgente = []
      this.selected.selectedStatus = []
      this.selected.selectedModulos = []
      this.selected.selectedUsuario = []
      this.selected.selectedClasificacion = []
      this.selected.selectedEspecialidad = []
      this.selected.selectedPrioridad = []
      this.selected.selectedDificultad = []
      this.selected.selectedErrores = []
      this.selected.selectedOficinas = []
      this.selected.selectedPerfiles = []
      this.selected.selectedFechaIni = null
      this.selected.selectedFechaFin = null
    },

    async seleccionar({ item }) {
      this.reporte = {
        IDReporte: item.IDReporte,
        titulo: item.Titulo,
        descripcion: item.Descripcion,
        sp: item.StoredProcedure,
      }
      this.filtrosShow = []
      const filtrosReporte = await this.$axios.$get(
        '/filtros/' + item.IDReporte
      )

      filtrosReporte.item.forEach((element) => {
        this.filtrosShow.push(element.IDFiltro)
      })
      this.limpiarFiltros()
      this.modalReporte = true
    },

    async generar() {
      this.selected.sp = this.reporte.sp

      this.selected.titulo = this.reporte.titulo

      try {
        const params = JSON.stringify(this.selected)
        await this.$axios({
          url: '/generarReportes/', // File URL Goes Here
          method: 'GET',

          params,
        }).then((res) => {
          const blob = new Blob([this.s2ab(res.data)], {
            type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
          })

          const url = window.URL.createObjectURL(blob)

          const link = document.createElement('a')
          const contentDisposition = res.headers['content-disposition']

          let fileName = 'unknown'
          if (contentDisposition) {
            let fileNameMatch = contentDisposition.match(/filename="(.+)"/)
            if (!fileNameMatch) {
              fileNameMatch = contentDisposition.match(/filename=(.+)/)
              if (fileNameMatch.length === 2) {
                fileName = fileNameMatch[1]
              }
            } else if (fileNameMatch.length === 2) {
              fileName = fileNameMatch[1]
            }
          }
          link.href = url
          link.setAttribute('download', fileName)
          document.body.appendChild(link)

          link.click()
          link.remove()
          window.URL.revokeObjectURL(url)
        })

        // console.log(testApis)
        this.modalReporte = false
      } catch (error) {
        this.modalReporte = false
        this.toast(
          'b-toaster-bottom-full',
          true,
          'Error al generar reporte, contacte al administrador del sistema'
        )
      }
    },

    cancelar() {
      this.modalReporte = false
      this.selected = {}
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
.modal-content {
  min-height: 450px;
}

/* .modal-dialog {
  width: 1080px;
} */
</style>
