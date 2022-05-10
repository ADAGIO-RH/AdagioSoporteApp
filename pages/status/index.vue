<template>
  <div class="cuerpo">
    <div class="card px-5 mx-3 mt-4">
      <!-- HEADER DE CATALOGO -->
      <div class="pt-2">
        <h3>Catálogo de Status</h3>
        <b-btn variant="primary" class="mb-3" @click="nueva"
          ><b-icon icon="plus"></b-icon> Nuevo Status</b-btn
        >
      </div>
      <!-- TABLA -->
      <div class="body">
        <b-table
          hover
          sticky-header="70vh"
          :items="list"
          :fields="fields"
          small
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
        :title="status.IDStatus ? 'Editando Status' : 'Nuevo Status'"
        hide-footer="true"
      >
        <form ref="form">
          <b-form-group
            id="fieldset-codigostatus"
            label="Código"
            label-for="input-codigostatus"
          >
            <b-form-input
              id="input-codigostatus"
              v-model="status.CodigoStatus"
              trim
            ></b-form-input>
          </b-form-group>
          <b-form-group
            id="fieldset-descripcionstatus"
            label="Descripción"
            label-for="input-descripcionstatus"
          >
            <b-form-input
              id="input-descripcionstatus"
              v-model="status.Descripcion"
              trim
            ></b-form-input>
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
  name: 'StatusPage',
  layout: 'navFooter',
  async asyncData({ $axios }) {
    const token = localStorage.getItem('token')
    $axios.defaults.headers.common['token-auth'] = token
    const testApis = await $axios.$get('/status/')
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
          key: 'CodigoStatus',
          label: 'Código',
          sortable: true,
          class: 'fontSizeSM',
        },
        {
          key: 'Descripcion',
          label: 'Descripción',
          sortable: false,
          class: 'fontSizeSM',
        },
      ],
      modalVisible: false,
      status: {},
    }
  },
  methods: {
    nueva() {
      this.status = {
        CodigoStatus: '',
        Descripcion: '',
      }
      this.modalVisible = true
    },
    modificar({ item }) {
      this.status = JSON.parse(JSON.stringify(item))
      this.modalVisible = true
    },
    async update() {
      const { list } = await this.$axios.$get('/status/')

      this.list = list
    },
    async eliminar({ item }) {
      try {
        await this.$axios.$delete('/status/' + item.IDStatus)
        this.$swal.fire('Eliminado', 'Registro eliminado', 'success')
        this.update()
      } catch (error) {
        this.$swal.fire('Error', 'No puedes eliminar este registro', 'error')
      }
    },
    validar(objet) {
      const faltantes = []
      const nulleable = []

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
      if (this.validar(this.status)) {
        try {
          await this.$axios.$post('/status/', this.status)
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
</style>
