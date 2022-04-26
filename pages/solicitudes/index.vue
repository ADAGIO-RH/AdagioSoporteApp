<template>
  <div class="px-2 mx-3 mt-3">
    <!-- HEADER-->
    <div class="pt-4 pb-4 px-3 mb-5 card">
      <h3>Modulo de Solicitudes</h3>
    </div>

    <div class="body card">
      <!-- FILTROS -->
      <div class="pt-2 pb-2 px-3 mb-3 fz-g">
        <h4>Filtros</h4>
        <div class="border-bottom mt-3"></div>
        <div class="mt-2 contenderoFiltros">
          <div class="filtro">
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
          <div class="filtro">
            Cliente
            <v-select
              v-model="selected.selectedCliente"
              label="text"
              multiple
              :placeholder="tagPlaceHolder"
              :options="options.optionsClientes"
              class="sizeBox"
            />
          </div>
          <div class="filtro">
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
          <div class="filtro">
            Modulo
            <v-select
              v-model="selected.selectedModulo"
              label="text"
              multiple
              :placeholder="tagPlaceHolder"
              :options="options.optionsModulo"
              class="sizeBox"
            />
          </div>
          <div class="filtro">
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
          <div class="filtro">
            Prioridad
            <v-select
              v-model="selected.selectedPrioridad"
              label="text"
              multiple
              :placeholder="tagPlaceHolder"
              :options="options.optionsPrioridad"
              class="sizeBox"
            />
          </div>
          <div class="filtro">
            Folio
            <b-form-input
              id="input-codigocliente"
              v-model="selected.selectedTicket[0]"
              placeholder="Escriba un Folio"
              trim
              @keypress="isNumber($event)"
            ></b-form-input>
          </div>
          <div class="filtro">
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
          <div class="filtro">
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

        <b-btn class="mt-3" @click="filtrar"
          ><b-icon icon="filter"></b-icon> Filtrar</b-btn
        >
        <b-btn variant="light" class="mt-3" @click="limpiarFiltros"
          ><b-icon icon="filter"></b-icon> Limpiar Filtros</b-btn
        >

        <div class="border-bottom mt-3"></div>
        <b-btn variant="primary" class="mt-3 widthButtonAdd" @click="nueva"
          ><b-icon icon="plus"></b-icon>Nueva Solicitud</b-btn
        >
      </div>

      <b-table
        hover
        striped
        sticky-header="30vh"
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
      v-model="modalModificar"
      title="Editando Solicitud"
      hide-footer="true"
      size="xl"
      scrollable
      hide-header-close="true"
    >
      <form ref="form" class="container">
        <div class="row justify-content-between">
          <div class="col-4">
            <b-form-group
              id="fieldset-folioticket"
              label="Folio"
              label-for="input-folioticket"
            >
              <b-form-input
                id="input-folioticket"
                v-model="ticket.ticket"
                disabled="true"
                trim
              ></b-form-input>
            </b-form-group>
          </div>
          <div class="col-4">
            <b-form-group
              id="fieldset-agenteticket"
              label="Agente"
              label-for="input-agenteticket"
            >
              <v-select
                v-model="ticket.agente"
                label="text"
                :placeholder="tagPlaceHolder"
                :options="options.optionsAgente"
                :reduce="(a) => a.value"
                class="sizeBox"
              />
            </b-form-group>
          </div>
        </div>
        <div class="row justify-content-between">
          <div class="col-4">
            <b-form-group
              id="fieldset-fechaticket"
              label="Fecha"
              label-for="input-fechaticket"
            >
              <b-input-group>
                <b-form-input
                  id="input-fechaticket"
                  v-model="ticket.fecha"
                  type="text"
                  placeholder="YYYY-MM-DD"
                  autocomplete="off"
                  class="sizeBox"
                  @keypress="isNumberDateTicket($event, 'fecha')"
                ></b-form-input>
                <b-input-group-append>
                  <b-form-datepicker
                    v-model="ticket.fecha"
                    button-only
                    right
                    aria-controls="input-fechaticket"
                    class="sizeBox"
                    @context="onContext"
                  ></b-form-datepicker>
                </b-input-group-append>
              </b-input-group>
            </b-form-group>
          </div>
          <div class="col-4">
            <b-form-group
              id="fieldset-statusticket"
              label="Status"
              label-for="input-statusticket"
            >
              <v-select
                v-model="ticket.status"
                label="text"
                :placeholder="tagPlaceHolder"
                :options="options.optionsStatus"
                :reduce="(a) => a.value"
                class="sizeBox"
              />
            </b-form-group>
          </div>
        </div>
        <div class="row justify-content-between">
          <div class="col-4">
            <b-form-group
              id="fieldset-horaticket"
              label="Hora"
              label-for="input-horaticket"
            >
              <b-form-timepicker
                v-model="ticket.hora"
                locale="en"
              ></b-form-timepicker>
            </b-form-group>
          </div>

          <div class="col-4">
            <b-form-group
              id="fieldset-prioridadticket"
              label="Prioridad"
              label-for="input-prioridadticket"
            >
              <v-select
                v-model="ticket.prioridad"
                label="text"
                :placeholder="tagPlaceHolder"
                :options="options.optionsPrioridad"
                :reduce="(a) => a.value"
                class="sizeBox"
              />
            </b-form-group>
          </div>
        </div>
        <div class="row justify-content-between">
          <div class="col-4">
            <b-form-group
              id="fieldset-usuarioticket"
              label="Usuario"
              label-for="input-usuarioticket"
            >
              <b-form-input
                id="input-usuarioticket"
                v-model="ticket.usuarioText"
                disabled="true"
                trim
              ></b-form-input>
            </b-form-group>
          </div>

          <div class="col-4">
            <b-form-group
              id="fieldset-dificultadticket"
              label="Dificultad"
              label-for="input-dificultadticket"
            >
              <v-select
                v-model="ticket.dificultad"
                label="text"
                :placeholder="tagPlaceHolder"
                :options="options.optionsDificultad"
                :reduce="(a) => a.value"
                class="sizeBox"
              />
            </b-form-group>
          </div>
        </div>
        <div class="row justify-content-between">
          <div class="col-4">
            <b-form-group
              id="fieldset-clienteticket"
              label="Cliente"
              label-for="input-clienteticket"
            >
              <b-form-input
                id="input-usuarioticket"
                v-model="ticket.clienteText"
                disabled="true"
                trim
              ></b-form-input>
            </b-form-group>
          </div>
          <div class="col-4">
            <b-form-group
              id="fieldset-moduloticket"
              label="Modulo"
              label-for="input-moduloticket"
            >
              <v-select
                v-model="ticket.modulo"
                label="text"
                :placeholder="tagPlaceHolder"
                :options="options.optionsModulo"
                :reduce="(a) => a.value"
                class="sizeBox"
              />
            </b-form-group>
          </div>
        </div>
        <div class="row justify-content-between">
          <div class="col-6">
            <b-form-group
              id="fieldset-usuarioticket"
              label="Descripcion"
              label-for="input-usuarioticket"
            >
              <b-form-textarea
                id="descripcionticket"
                v-model="ticket.descripcion"
                placeholder="Descripcion"
                rows="10"
                max-rows="10"
              ></b-form-textarea>
            </b-form-group>
          </div>
          <div class="col-4">
            <b-form-group
              id="fieldset-errorticket"
              label="Error"
              label-for="input-errorticket"
            >
              <v-select
                v-model="ticket.error"
                label="text"
                :placeholder="tagPlaceHolder"
                :options="options.optionsErrores"
                :reduce="(a) => a.value"
                class="sizeBox"
              />
            </b-form-group>
            <b-form-group
              id="fieldset-clasificacionticket"
              label="Clasificacion"
              label-for="input-clasificacionticket"
            >
              <v-select
                v-model="ticket.clasificacion"
                label="text"
                :placeholder="tagPlaceHolder"
                :options="options.optionsClasificacion"
                :reduce="(a) => a.value"
                class="sizeBox"
              />
            </b-form-group>
            <b-form-group
              id="fieldset-duracionticket"
              label="Duracion"
              label-for="input-duracionticket"
            >
              <b-form-input
                id="input-duracionticket"
                v-model="ticket.duracionAgente"
                type="number"
                step="30"
                trim
              ></b-form-input>
            </b-form-group>
          </div>
        </div>
        <div class="row justify-content-between">
          <div class="col-6">
            <b-form-group
              id="fieldset-descripcionsolucionticket"
              label="Descripcion de la Solución"
              label-for="input-descripcionsolucionticket"
            >
              <b-form-textarea
                id="descripcionsolucionticket"
                v-model="ticket.descripcionSolucion"
                placeholder="Solución"
                rows="12"
                max-rows="12"
              ></b-form-textarea>
            </b-form-group>
          </div>
          <div class="col-4">
            <b-form-group
              id="fieldset-adjuntararchivoticket"
              label="Subir archivos"
              label-for="input-adjuntararchivoticket"
            >
              <b-form-file
                v-model="files"
                placeholder="Elige o arrastra tu archivo"
                drop-placeholder=""
                multiple
              ></b-form-file>
            </b-form-group>
            <b-table
              hover
              striped
              sticky-header="20vh"
              :items="filesShow"
              :fields="fieldsFiles"
              small
              class="mt-0"
            >
              <template #cell(actions)="row">
                <b-dropdown
                  dropright
                  toggle-class="text-decoration-none"
                  no-caret
                  size="sm"
                  variant="info"
                >
                  <template #button-content>
                    <b-icon icon="list"></b-icon>
                  </template>

                  <b-dropdown-item @click="descargarArchivo(row)">
                    <b-icon icon="file-earmark-arrow-down"></b-icon> Descargar
                  </b-dropdown-item>
                  <b-dropdown-item @click="confirmarEliminarArchivo(row)">
                    <b-icon icon="trash"></b-icon> Eliminar
                  </b-dropdown-item>
                </b-dropdown>
              </template>
            </b-table>
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
    <b-modal
      v-model="modalNuevo"
      title="Nueva Solicitud"
      hide-footer="true"
      size="xl"
      scrollable
      hide-header-close="true"
    >
      <form ref="form" class="container">
        <div class="row justify-content-between">
          <div class="col-4">
            <b-form-group
              id="fieldset-fechaticket"
              label="Fecha"
              label-for="input-fechaticket"
            >
              <b-input-group>
                <b-form-input
                  id="input-fechaticket"
                  v-model="ticket.fecha"
                  type="text"
                  placeholder="YYYY-MM-DD"
                  autocomplete="off"
                  class="sizeBox"
                  required
                  @keypress="isNumberDateTicket($event, 'fecha')"
                ></b-form-input>
                <b-input-group-append>
                  <b-form-datepicker
                    v-model="ticket.fecha"
                    button-only
                    right
                    aria-controls="input-fechaticket"
                    class="sizeBox"
                    @context="onContext"
                  ></b-form-datepicker>
                </b-input-group-append>
              </b-input-group>
            </b-form-group>
          </div>
          <div class="col-4">
            <b-form-group
              id="fieldset-agenteticket"
              label="Agente"
              label-for="input-agenteticket"
            >
              <v-select
                v-model="ticket.agente"
                label="text"
                :placeholder="tagPlaceHolder"
                :options="options.optionsAgente"
                :reduce="(a) => a.value"
                class="sizeBox"
              />
            </b-form-group>
          </div>
        </div>
        <div class="row justify-content-between">
          <div class="col-4">
            <b-form-group
              id="fieldset-horaticket"
              label="Hora"
              label-for="input-horaticket"
            >
              <b-form-timepicker
                v-model="ticket.hora"
                locale="en"
              ></b-form-timepicker>
            </b-form-group>
          </div>

          <div class="col-4">
            <b-form-group
              id="fieldset-statusticket"
              label="Status"
              label-for="input-statusticket"
            >
              <v-select
                v-model="ticket.status"
                label="text"
                :placeholder="tagPlaceHolder"
                :options="options.optionsStatus"
                :reduce="(a) => a.value"
                class="sizeBox"
              />
            </b-form-group>
          </div>
        </div>
        <div class="row justify-content-between">
          <div class="col-4">
            <b-form-group
              id="fieldset-usuarioticket"
              label="Usuarios"
              label-for="input-usuarioticket"
            >
              <!-- <v-select
                v-model="ticket.usuario"
                label="text"
                :placeholder="tagPlaceHolder"
                :options="options.optionsUsuarios"
                :reduce="(a) => a.value"
                class="sizeBox"
              /> -->

              <b-form-input
                id="input-usuarioticket"
                value="CAPTURA"
                disabled="true"
                trim
              ></b-form-input>
            </b-form-group>
          </div>

          <div class="col-4">
            <b-form-group
              id="fieldset-prioridadticket"
              label="Prioridad"
              label-for="input-prioridadticket"
            >
              <v-select
                v-model="ticket.prioridad"
                label="text"
                :placeholder="tagPlaceHolder"
                :options="options.optionsPrioridad"
                :reduce="(a) => a.value"
                class="sizeBox"
              />
            </b-form-group>
          </div>
        </div>
        <div class="row justify-content-between">
          <div class="col-4">
            <b-form-group
              id="fieldset-clienteticket"
              label="Cliente"
              label-for="input-clienteticket"
            >
              <v-select
                v-model="ticket.cliente"
                label="text"
                :placeholder="tagPlaceHolder"
                :options="options.optionsClientes"
                :reduce="(a) => a.value"
                class="sizeBox"
              />
            </b-form-group>
          </div>
          <div class="col-4">
            <b-form-group
              id="fieldset-dificultadticket"
              label="Dificultad"
              label-for="input-dificultadticket"
            >
              <v-select
                v-model="ticket.dificultad"
                label="text"
                :placeholder="tagPlaceHolder"
                :options="options.optionsDificultad"
                :reduce="(a) => a.value"
                class="sizeBox"
              />
            </b-form-group>
          </div>
        </div>
        <div class="row justify-content-between">
          <div class="col-6">
            <b-form-group
              id="fieldset-descripcionticket"
              label="Descripcion"
              label-for="input-descripcionticket"
            >
              <b-form-textarea
                id="descripcionticket"
                v-model="ticket.descripcion"
                placeholder="Descripcion"
                rows="12"
                max-rows="12"
              ></b-form-textarea>
            </b-form-group>
          </div>
          <div class="col-4">
            <b-form-group
              id="fieldset-moduloticket"
              label="Modulo"
              label-for="input-moduloticket"
            >
              <v-select
                v-model="ticket.modulo"
                label="text"
                :placeholder="tagPlaceHolder"
                :options="options.optionsModulo"
                :reduce="(a) => a.value"
                class="sizeBox"
              />
            </b-form-group>
            <b-form-group
              id="fieldset-errorticket"
              label="Error"
              label-for="input-errorticket"
            >
              <v-select
                v-model="ticket.error"
                label="text"
                :placeholder="tagPlaceHolder"
                :options="options.optionsErrores"
                :reduce="(a) => a.value"
                class="sizeBox"
              />
            </b-form-group>
            <b-form-group
              id="fieldset-clasificacionticket"
              label="Clasificacion"
              label-for="input-clasificacionticket"
            >
              <v-select
                v-model="ticket.clasificacion"
                label="text"
                :placeholder="tagPlaceHolder"
                :options="options.optionsClasificacion"
                :reduce="(a) => a.value"
                class="sizeBox"
              />
            </b-form-group>
            <b-form-group
              id="fieldset-duracionticket"
              label="Duracion"
              label-for="input-duracionticket"
            >
              <b-form-input
                id="input-duracionticket"
                v-model="ticket.duracionAgente"
                type="number"
                step="30"
                trim
              ></b-form-input>
            </b-form-group>
          </div>
        </div>
        <div class="row justify-content-between">
          <div class="col-6">
            <b-form-group
              id="fieldset-descripcionsolucionticket"
              label="Descripcion de la Solución"
              label-for="input-descripcionsolucionticket"
            >
              <b-form-textarea
                id="descripcionsolucionticket"
                v-model="ticket.descripcionSolucion"
                placeholder="Solución"
                rows="12"
                max-rows="12"
              ></b-form-textarea>
            </b-form-group>
          </div>
          <div class="col-4">
            <b-form-group
              id="fieldset-adjuntararchivoticket"
              label="Subir archivos"
              label-for="input-adjuntararchivoticket"
            >
              <b-form-file
                v-model="files"
                placeholder="Elige o arrastra tu archivo"
                drop-placeholder=""
                multiple
              ></b-form-file>
            </b-form-group>
            <b-table
              hover
              striped
              sticky-header="20vh"
              :items="filesShow"
              :fields="fieldsFiles"
              small
              class="mt-0"
            >
              <template #cell(actions)="row">
                <b-dropdown
                  dropright
                  toggle-class="text-decoration-none"
                  no-caret
                  size="sm"
                  variant="info"
                >
                  <template #button-content>
                    <b-icon icon="list"></b-icon>
                  </template>

                  <b-dropdown-item @click="descargarArchivo(row)">
                    <b-icon icon="file-earmark-arrow-down"></b-icon> Descargar
                  </b-dropdown-item>
                  <b-dropdown-item @click="confirmarEliminarArchivo(row)">
                    <b-icon icon="trash"></b-icon> Eliminar
                  </b-dropdown-item>
                </b-dropdown>
              </template>
            </b-table>
          </div>
        </div>
      </form>

      <footer class="modal-footer">
        <b-button class="mt-2" variant="primary" @click="guardarNuevo"
          >Guardar</b-button
        >
        <b-button class="mt-2" variant="danger" @click="cancelarNuevo"
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
  name: 'SolicitudesPage',
  layout: 'navFooter',
  async asyncData({ $axios }) {
    const selected = {
      selectedTicket: [],
      selectedCliente: [],
      selectedAgente: [],
      selectedStatus: [],
      selectedUsuario: [],
      selectedModulo: [],
      selectedClasificacion: [],
      selectedPrioridad: [],
      selectedFechaIni: null,
      selectedFechaFin: null,
    }

    const token = localStorage.getItem('token')
    $axios.defaults.headers.common['token-auth'] = token

    const params = JSON.stringify(selected)

    const testApis = await $axios.$get('/solicitudes/', {
      params,
    })

    const { list } = testApis
    // delete $axios.defaults.headers.common['token-auth']
    return { list }
  },
  data() {
    return {
      selected: {
        selectedTicket: [],
        selectedCliente: [],
        selectedAgente: [],
        selectedStatus: [],
        selectedModulo: [],
        selectedUsuario: [],
        selectedClasificacion: [],
        selectedPrioridad: [],
        selectedFechaIni: null,
        selectedFechaFin: null,
      },

      options: {
        optionsClientes: [],
        optionsStatus: [],
        optionsModulo: [],
        optionsClasificacion: [],
        optionsPrioridad: [],
        optionsAgente: [],
        optionsDificultad: [],
        optionsErrores: [],
        optionsUsuarios: [],
      },

      fields: [
        {
          key: 'actions',
          label: '',
          class: 'actionsStyle',
        },
        {
          key: 'IDTicket',
          label: 'Folio Ticket',
          sortable: true,
          class: 'fontSizeSM',
        },
        {
          key: 'FECHA',
          label: 'Fecha',
          sortable: false,
          class: 'fontSizeSM',
        },
        {
          key: 'CLIENTE',
          label: 'Cliente',
          sortable: false,
          class: 'fontSizeSM',
        },
        {
          key: 'USUARIO',
          label: 'Usuario',
          sortable: false,
          class: 'fontSizeSM',
        },
        {
          key: 'MODULO',
          label: 'Modulo',
          sortable: false,
          class: 'fontSizeSM',
        },

        {
          key: 'CLASIFICACION',
          label: 'Clasificacion',
          sortable: false,
          class: 'fontSizeSM',
        },
        {
          key: 'PRIORIDAD',
          label: 'Prioridad',
          sortable: false,
          class: 'fontSizeSM',
        },
        {
          key: 'STATUS',
          label: 'Status',
          sortable: false,
          class: 'fontSizeSM',
        },
        {
          key: 'AGENTE',
          label: 'Agente',
          sortable: false,
          class: 'fontSizeSM agenteBG ',
        },
      ],
      fieldsFiles: [
        {
          key: 'actions',
          label: '',
          class: 'actionsStyle',
        },
        {
          key: 'name',
          label: 'Archivos',
        },
      ],
      modalModificar: false,
      modalNuevo: false,
      ticket: {},
      ticketModal: {},
      tagPlaceHolder: 'Seleccione una opción',
      files: [],
      filesShow: {},
    }
  },

  async mounted() {
    // const { list } = await this.$axios.$get('/clientes/')
    const cliente = await this.$axios.$get('/clientes/')
    const status = await this.$axios.$get('/status/')
    const modulo = await this.$axios.$get('/modulos/')
    const clasificacion = await this.$axios.$get('/clasificaciones/')
    const prioridades = await this.$axios.$get('/prioridades/')
    const agentes = await this.$axios.$get('/agentes/')
    const dificultad = await this.$axios.$get('/dificultades/')
    const errores = await this.$axios.$get('/errores/')
    const usuarios = await this.$axios.$get('/usuarios/')
    const listaClientes = cliente.list
    const listaStatus = status.list
    const listaModulos = modulo.list
    const listaClasificacion = clasificacion.list
    const listaPrioridades = prioridades.list
    const listaAgentes = agentes.list
    const listaDificultades = dificultad.list
    const listaErrores = errores.list
    const listaUsuarios = usuarios.list
    listaClientes.forEach((element) => {
      this.options.optionsClientes.push({
        value: element.IDCliente,
        text: element.NombreComercial,
      })
    })
    listaStatus.forEach((element) => {
      this.options.optionsStatus.push({
        value: element.IDStatus,
        text: element.Descripcion,
      })
    })
    listaModulos.forEach((element) => {
      this.options.optionsModulo.push({
        value: element.IDModulo,
        text: element.Descripcion,
      })
    })
    listaClasificacion.forEach((element) => {
      this.options.optionsClasificacion.push({
        value: element.IDClasificacion,
        text: element.Descripcion,
      })
    })
    listaPrioridades.forEach((element) => {
      this.options.optionsPrioridad.push({
        value: element.IDPrioridad,
        text: element.Descripcion,
      })
    })
    listaAgentes.forEach((element) => {
      this.options.optionsAgente.push({
        value: element.IDAgente,
        text: element.Nombre,
      })
    })
    listaDificultades.forEach((element) => {
      this.options.optionsDificultad.push({
        value: element.IDDificultad,
        text: element.Descripcion,
      })
    })
    listaErrores.forEach((element) => {
      this.options.optionsErrores.push({
        value: element.IDTipoError,
        text: element.Descripcion,
      })
    })
    listaUsuarios.forEach((element) => {
      this.options.optionsUsuarios.push({
        value: element.IDUsuario,
        text: element.Nombre,
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

    async limpiarFiltros() {
      this.selected.selectedTicket = []
      this.selected.selectedAgente = []
      this.selected.selectedCliente = []
      this.selected.selectedStatus = []
      this.selected.selectedModulo = []
      this.selected.selectedClasificacion = []
      this.selected.selectedPrioridad = []
      this.selected.selectedFechaIni = null
      this.selected.selectedFechaFin = null

      const selected = {
        selectedTicket: [],
        selectedCliente: [],
        selectedAgente: [],
        selectedUsuario: [],
        selectedStatus: [],
        selectedModulo: [],
        selectedClasificacion: [],
        selectedPrioridad: [],
        selectedFechaIni: null,
        selectedFechaFin: null,
      }

      const params = JSON.stringify(selected)
      const { list } = await this.$axios.$get('/solicitudes/', {
        params,
      })
      this.list = list
    },
    async update() {
      const params = JSON.stringify(this.selected)

      const { list } = await this.$axios.$get('/solicitudes/', {
        params,
      })

      // const { list } = await this.$axios.$get('/solicitudes/', this.selected)

      this.list = list
    },
    filtrar() {
      this.update()
    },
    nueva() {
      console.log(this.filesShow)
      this.filesShow = {}
      console.log(this.filesShow)
      this.ticket = {
        ticket: 0,
        agente: null,
        status: null,
        usuario: null,
        prioridad: null,
        cliente: null,
        dificultad: null,
        modulo: null,
        error: null,
        clasificacion: null,
        duracionAgente: null,
        descripcion: null,
        descripcionSolucion: null,
        fecha: this.fecha(),
        hora: this.hora(),
      }
      this.file = []
      this.modalNuevo = true
    },
    descargarArchivo({ item }) {
      this.$axios({
        url: '/files/' + item.name, // File URL Goes Here
        method: 'GET',
        responseType: 'blob', // blob response
        headers: { id: this.ticket.ticket }, // folio de ticket, ruta carpeta
      }).then((res) => {
        const url = window.URL.createObjectURL(
          new Blob([res.data], { type: res.data.type })
        )
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
    },
    modificar({ item }) {
      this.filesShow = {}
      this.ticket = {
        ticket: item.IDTicket,
        agente: item.IDAgente,
        status: item.IDStatus,
        usuarioText: item.USUARIO,
        prioridad: item.IDPrioridad,
        descripcion: item.Descripcion,
        descripcionSolucion: item.DescripcionSolucion,
        dificultad: item.IDDificultad,
        modulo: item.IDModulo,
        clienteText: item.CLIENTE,
        error: item.IDTipoError,
        duracionAgente: item.DuracionAgente,
        clasificacion: item.IDClasificacion,
        fecha: item.FECHA,
        hora: item.HORA,
      }
      this.updateTableFiles(item)

      this.modalModificar = true
    },

    async updateTableFiles() {
      const objeto = await this.$axios.$get('/files/', {
        headers: {
          id: this.ticket.ticket,
        },
      })
      this.filesShow = objeto.files
    },

    async eliminar({ item }) {
      try {
        await this.$axios.$delete('/solicitudes/' + item.IDTicket)
        this.$swal.fire('Eliminado', 'Registro Eliminado', 'success')
        this.update()
      } catch (error) {
        this.$swal.fire('Error', 'No se puede eliminar este registro', 'error')
      }
    },
    eliminarArchivo({ item }) {
      try {
        this.$axios({
          url: '/files/' + item.name, // File URL Goes Here
          method: 'delete',
          headers: { id: this.ticket.ticket }, // folio de ticket, ruta carpeta
        })
        this.updateTableFiles()
        this.modalModificar = false
      } catch (error) {}
    },
    async guardar() {
      if (this.validar(this.ticket)) {
        await this.$axios.$post('/solicitudes/', this.ticket)
        this.update()
        if (this.files !== []) {
          this.uploadFiles(this.files)
          this.files = []
        }
        this.update()
        this.modalModificar = false
      }
    },
    cancelar() {
      this.modalModificar = false
      this.files = []
      this.update()
    },
    cancelarNuevo() {
      this.modalNuevo = false
      this.files = []
      this.update()
    },
    async guardarNuevo() {
      this.update()

      if (this.validar(this.ticket)) {
        const resp = await this.$axios.$post('/solicitudes/', this.ticket)
        const IDTicketNuevo = resp.item[0]?.IDTicketNuevo
        this.ticket.ticket = IDTicketNuevo
        if (this.files !== []) {
          this.uploadFiles(this.files)
          this.files = []
        }
        this.update()
        this.modalNuevo = false
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
    confirmarEliminarArchivo(row) {
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
            this.$swal.fire(
              'Eliminado',
              'El registro ha sido eliminado',
              'success'
            )
            this.eliminarArchivo(row)
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
      const nulleable = [
        'agente',
        'usuario',
        'prioridad',
        'dificultad',
        'modulo',
        'error',
        'clasificacion',
        'duracionAgente',
        'descripcionSolucion',
      ]
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
