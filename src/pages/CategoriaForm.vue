<template>
  <q-page class="flex column" padding>

    <p class="text-h3">{{categoria.nom}}</p>

    <q-input v-model="categoria.nom" label="Nom" />

    <q-btn color="primary" icon="save" label="Desar" @click="save" />

  </q-page>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import {CategoriaConvalidacio} from "src/model/CategoriaConvalidacio";
import {ConvalidacioService} from "src/service/ConvalidacioService";


export default defineComponent({
  name: 'CategoriaConvalidacioForm',
  data() {
    return {
      categoria: {} as CategoriaConvalidacio
    }
  },
  async created() {
    await this.get();
  },
  methods: {
    get: async function () {
      /*const dialog = this.$q.dialog({
        message: 'Carregant...',
        progress: true, // we enable default settings
        persistent: true, // we want the user to not be able to close it
        ok: false // we want the user to not be able to close it
      });*/

      const id:string = (this.$route.params.id)?this.$route.params.id+'':'';

      if(id && id!='') {
        console.log(id)
        const categoria:CategoriaConvalidacio = await ConvalidacioService.getCategoriaById(parseInt(id));

        this.categoria = categoria;
      }


      //dialog.hide();
    },
    save: async function(){
      const dialog = this.$q.dialog({
        message: 'Carregant...',
        progress: true, // we enable default settings
        persistent: true, // we want the user to not be able to close it
        ok: false // we want the user to not be able to close it
      })

      await ConvalidacioService.saveCategoria(this.categoria);
      dialog.hide();

      //Redirect
      await this.$router.push('/categories');
    }
  }
})
</script>
