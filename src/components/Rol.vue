<template>
  <v-layout align-start>
      <v-flex>
            <v-toolbar flat color="#b2dfdb">
              <v-toolbar-title>Roles</v-toolbar-title>
              <v-divider
                  class="mx-2"
                  inset
                  vertical
              ></v-divider>
              <v-spacer></v-spacer>
              <v-text-field class="text-xs-center" v-model="search" append-icon="search" label="Búsqueda" single-line hide-details></v-text-field>
              <v-spacer></v-spacer>
            </v-toolbar>
            <!-- DATA TABLE STARTS HERE -->
            <v-data-table
            :headers="headers"
            :items="roles"
            :search="search"
            class="elevation-3"
            >
            <template slot="items" slot-scope="props">
                  <td class="text-xs-left">{{ props.item.nombre }}</td>
                  <td class="text-xs-left">{{ props.item.descripcion }}</td>
                  <td> 
                    <div v-if="props.item.condicion">
                        <span class="blue--text">Activo</span>
                    </div>
                    <div v-else>
                        <span class="red--text">Inactivo</span>
                    </div>
                  </td>
            </template>
            <template slot="no-data">
                <v-btn color="primary" @click="listar">Resetear</v-btn>
            </template>
            </v-data-table>
      </v-flex>
  </v-layout>
</template>

<script>
  import axios from 'axios'

  export default {
    data(){
      return {
          roles:[],
          dialog: false,
          headers: [
            { text: 'Nombres', value: 'nombre' },
            { text: 'Descripción', value: 'descripcion',sorteable: false },
            { text: 'Estado', value: 'condicion',sorteable: false }
          ],
          search: '',

      }
    },
    computed: {
    },

    watch: {
    },

    created () {
      this.listar();
    },

    methods: {

      listar(){
        let me = this;
        axios.get('api/Roles/Listar').then(function(response){
          //console.log(response)
          me.roles=response.data;
        }).catch(function(error){
          //console.log(error)
        })
      },    

    }
  }
</script>
<style>
table th + th { border-left:1px solid #dddddd; }
table td + td { border-left:1px solid #dddddd; }
</style>


