<template>
  <q-page class="flex column" padding>
    <div>
      <q-btn-group push class="q-mb-lg q-mr-lg">
        <q-btn  color="primary" label="Nova categoria" icon="add" :to="'/categoria'" v-if="rolsUser.find(rol=>rol===rols.ADMINISTRADOR || rol===rols.DIRECTOR || rol===rols.CAP_ESTUDIS)"/>
      </q-btn-group>
    </div>

    <q-table
      title="Categories dels ítems de convalidació"
      :rows="categories"
      :columns="columnes"
      row-key="id"
      selection="single"
      :filter="filter"
      v-model:selected="selected"
    >
      <template v-slot:top-right>
        <q-input borderless dense debounce="300" v-model="filter" placeholder="Cerca">
          <template v-slot:append>
            <q-icon name="search" />
          </template>
        </q-input>
      </template>
      <template v-slot:body-cell-accions="props">
        <q-td :props="props">
          <div>
            <q-btn-group push>
              <q-btn icon="edit" color="primary" dense :to="'/categoria/'+props.value">
                <q-tooltip>
                  Edita
                </q-tooltip>
              </q-btn>
              <q-btn icon="delete" color="primary" dense @click="esborrarCategoria(props.value)">
                <q-tooltip>
                  Esborra
                </q-tooltip>
              </q-btn>
            </q-btn-group>
          </div>
        </q-td>
      </template>
    </q-table>

  </q-page>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import {ConvalidacioService} from 'src/service/ConvalidacioService';
import {CategoriaConvalidacio} from "src/model/CategoriaConvalidacio";
import {QTableColumn} from "quasar";
import {Rol} from "src/model/Rol";


export default defineComponent({
  name: 'PageGrupCorreu',
  data() {
    return {
      categories: [] as CategoriaConvalidacio[],
      columnes: [] as QTableColumn[],
      selected: [],
      filter: '',
      rolsUser: (localStorage.getItem("rol"))?JSON.parse(localStorage.getItem("rol")!):[],
      rols: Rol
    }
  },
  created() {
    this.get();
  },
  methods: {
    get: async function () {
      this.columnes = [
        {
          name: 'nom',
          required: true,
          label: 'Nom',
          align: 'left',
          field: row => row.nom,
          sortable: true
        },
        {
          name: 'accions',
          required: true,
          label: 'Accions',
          align: 'center',
          field: row => row.id,
          sortable: true
        }
      ]

      const categories:Array<CategoriaConvalidacio>= await ConvalidacioService.getCategories();

      this.categories = categories;
    },
    esborrarCategoria(id:number){
      this.$q.dialog({
        title: 'Confirm',
        message: 'Vol eliminar aquesta categoria?',
        ok: "D'acord",
        cancel: "Cancel·la",
        persistent: true
      }).onOk(async () => {
        console.log('>>>> OK',id)
        await ConvalidacioService.esborrarCategoria(id);
        //Refresh data
        window.location.reload();
      })
    }
  }
})
</script>
