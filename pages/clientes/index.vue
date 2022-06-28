<template>
  <div class="card px-5 mx-3 mt-4">
    <!-- HEADER DE CATALOGO -->
    <div class="pt-2">
      <h3>Catálogo de Clientes</h3>
      <b-btn variant="primary" class="mb-3" @click="nueva"
        ><b-icon icon="plus"></b-icon> Nuevo Cliente</b-btn
      >
    </div>
    <!-- TABLA -->
    <div class="body">
      <b-table hover sticky-header="70vh" :items="list" :fields="fields" small>
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
      :title="cliente.IDCliente ? 'Editando Cliente' : 'Nuevo'"
      hide-footer="true"
    >
      <form ref="form">
        <b-form-group
          id="fieldset-codigocliente"
          label="Código"
          label-for="input-codigocliente"
        >
          <b-form-input
            id="input-codigocliente"
            v-model="cliente.CodigoCliente"
            trim
          ></b-form-input>
        </b-form-group>
        <b-form-group
          id="fieldset-nombrecomercial"
          label="Nombre Comercial"
          label-for="input-nombrecomercial"
        >
          <b-form-input
            id="input-nombrecomercial"
            v-model="cliente.NombreComercial"
            trim
          ></b-form-input>
        </b-form-group>
        <b-form-group
          id="fieldset-urlsitio"
          label="URL Sitio"
          label-for="input-urlsitio"
        >
          <b-form-input
            id="input-urlsitio"
            v-model="cliente.URLSitio"
            trim
          ></b-form-input>
        </b-form-group>
        <b-form-group
          id="fieldset-horassoporte"
          label="Horas de Soporte"
          label-for="input-horassoporte"
        >
          <b-form-input
            id="input-horassoporte"
            v-model="cliente.HorasSoportePoliza"
            v-b-tooltip.hover.bottomright="
              'Numero de horas indicadas en la Poliza del Cliente'
            "
            trim
            @keypress="isNumber($event)"
          ></b-form-input>
        </b-form-group>

        <b-form-group
          id="fieldset-Contrato"
          label="Contrato Cliente"
          label-for="input-horassoporte"
        >
          <div class="col-13">
            <b-form-group
              id="fieldset-adjuntararchivoticket"
              label-for="input-adjuntararchivoticket"
            >
              <b-form-file
                v-model="files"
                placeholder="Elige o arrastra tu archivo"
                drop-placeholder=""
                multiple
              ></b-form-file>
            </b-form-group>
          </div>
        </b-form-group>
        <b-form-group>
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
        </b-form-group>
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

Vue.use(BootstrapVue)
Vue.use(BootstrapVueIcons)
Vue.use(VueSweetalert2)

export default {
  name: 'ClientesPage',
  layout: 'navFooter',
  async asyncData({ $axios }) {
    const token = localStorage.getItem('token')
    $axios.defaults.headers.common['token-auth'] = token
    const testApis = await $axios.$get('/clientes/')
    const { list } = testApis
    // delete $axios.defaults.headers.common['token-auth']
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
          key: 'CodigoCliente',
          label: 'Código',
          sortable: true,
          class: 'fontSizeSM',
        },
        {
          key: 'NombreComercial',
          label: 'Nombre Comercial',
          sortable: false,
          class: 'fontSizeSM',
        },
        {
          key: 'URLSitio',
          label: 'URL',
          sortable: false,
          class: 'fontSizeSM',
        },
        {
          key: 'HorasSoportePoliza',
          label: 'Horas Soporte',
          sortable: false,
          class: 'fontSizeSM',
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
      modalVisible: false,
      cliente: {},
    }
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
    nueva() {
      this.cliente = {
        CodigoCliente: '',
        NombreComercial: '',
        URLSitio: '',
        HorasSoportePoliza: null,
      }
      this.modalVisible = true
    },
    modificar({ item }) {
      this.cliente = JSON.parse(JSON.stringify(item))

      this.modalVisible = true
    },
    async update() {
      const { list } = await this.$axios.$get('/clientes/')

      this.list = list
    },
    async eliminar({ item }) {
      try {
        await this.$axios.$delete('/clientes/' + item.IDCliente)
        this.$swal.fire('Eliminado', 'Registro eliminado', 'success')
        this.update()
      } catch (error) {
        this.$swal.fire('Error', 'No puedes eliminar este registro', 'error')
      }
    },
    validar(objet) {
      const faltantes = []
      const nulleable = ['URLSitio', 'HorasSoportePoliza']
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
    async guardar() {
      if (this.validar(this.cliente)) {
        try {
          await this.$axios.$post('/clientes/', this.cliente)
          this.modalVisible = false
          this.update()
        } catch (error) {
          this.$swal.fire('Error', 'Código existente', 'error')
        }
      }
    },
    cancelar() {
      this.modalVisible = false
      this.update()
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
.fontSizeSM {
  font-size: 12px;
}
</style>
