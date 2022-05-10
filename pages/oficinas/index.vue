<template>
  <div class="cuerpo">
    <div class="card px-5 mx-3 mt-4">
      <!-- HEADER DE CATALOGO -->
      <div class="pt-2">
        <h3>Catálogo de Oficinas</h3>
        <b-btn variant="primary" class="mb-3" @click="nueva"
          ><b-icon icon="plus"></b-icon> Nueva Oficina</b-btn
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
        :title="oficina.IDOficina ? 'Editando Oficina' : 'Nuevo'"
        hide-footer="true"
      >
        <form ref="form">
          <b-form-group
            id="fieldset-codigooficina"
            label="Código"
            label-for="input-codigooficina"
          >
            <b-form-input
              id="input-codigoficina"
              v-model="oficina.CodigoOficina"
              trim
            ></b-form-input>
          </b-form-group>
          <b-form-group
            id="fieldset-descripcionoficina"
            label="Descripción"
            label-for="input-descripcioficina"
          >
            <b-form-input
              id="input-descripcionoficina"
              v-model="oficina.Descripcion"
              trim
            ></b-form-input>
          </b-form-group>
          <b-form-group
            id="fieldset-direccionoficinaa"
            label="Dirección"
            label-for="input-direccionoficina"
          >
            <b-form-input
              id="input-direccionoficina"
              v-model="oficina.Direccion"
              trim
            ></b-form-input>
          </b-form-group>
          <b-form-group
            id="fieldset-telefonooficinaa"
            label="Telefono"
            label-for="input-telefonooficina"
          >
            <b-form-input
              id="input-telefonooficina"
              v-model="oficina.Telefono"
              type="tel"
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
  name: 'OficinasPage',
  layout: 'navFooter',
  async asyncData({ $axios }) {
    const token = localStorage.getItem('token')
    $axios.defaults.headers.common['token-auth'] = token
    const testApis = await $axios.$get('/oficinas/')
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
          key: 'CodigoOficina',
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
        {
          key: 'Direccion',
          label: 'Dirección',
          sortable: false,
          class: 'fontSizeSM',
        },
        {
          key: 'Telefono',
          label: 'Telefono',
          sortable: false,
          class: 'fontSizeSM',
        },
      ],
      modalVisible: false,
      oficina: {},
    }
  },
  methods: {
    nueva() {
      this.oficina = {
        CodigoOficina: '',
        Descripcion: '',
        Direccion: '',
        Telefono: '',
      }
      this.modalVisible = true
    },
    modificar({ item }) {
      this.oficina = JSON.parse(JSON.stringify(item))
      this.modalVisible = true
    },
    async update() {
      const { list } = await this.$axios.$get('/oficinas/')

      this.list = list
    },
    async eliminar({ item }) {
      try {
        await this.$axios.$delete('/oficinas/' + item.IDOficina)
        this.$swal.fire('Eliminado', 'Registro eliminado', 'success')
        this.update()
      } catch (error) {
        this.$swal.fire('Error', 'No puedes eliminar este registro', 'error')
      }
    },
    validar(objet) {
      const faltantes = []
      const nulleable = ['Direccion', 'Telefono']

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
      if (this.validar(this.oficina)) {
        try {
          await this.$axios.$post('/oficinas/', this.oficina)
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
