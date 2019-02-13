<template>
  <v-layout align-start>
      <v-flex>
            <v-toolbar flat color="#b2dfdb">
              <v-toolbar-title>Categorías</v-toolbar-title>
              <v-divider
                  class="mx-2"
                  inset
                  vertical
              ></v-divider>
              <v-spacer></v-spacer>
              <v-text-field class="text-xs-center" v-model="search" append-icon="search" label="Búsqueda" single-line hide-details></v-text-field>
              <v-spacer></v-spacer>
              <v-dialog v-model="dialog" max-width="500px">
                  <v-btn slot="activator" color="primary" dark class="mb-2">Nueva Categoría</v-btn>
                  <v-card>
                    <v-card-title class="justify-center">
                        <span class="headline">{{ formTitle }}</span>
                    </v-card-title>
                    <v-card-text>
                        <v-container grid-list-md>
                        <v-layout wrap>
                            <v-flex xs12 sm6 md4>
                            <v-text-field v-model="editedItem.name" label="Dessert name"></v-text-field>
                            </v-flex>
                            <v-flex xs12 sm6 md4>
                            <v-text-field v-model="editedItem.calories" label="Calories"></v-text-field>
                            </v-flex>
                            <v-flex xs12 sm6 md4>
                            <v-text-field v-model="editedItem.fat" label="Fat (g)"></v-text-field>
                            </v-flex>
                            <v-flex xs12 sm6 md4>
                            <v-text-field v-model="editedItem.carbs" label="Carbs (g)"></v-text-field>
                            </v-flex>
                            <v-flex xs12 sm6 md4>
                            <v-text-field v-model="editedItem.protein" label="Protein (g)"></v-text-field>
                            </v-flex>
                        </v-layout>
                        </v-container>
                    </v-card-text>

                    <v-card-actions>
                        <v-spacer></v-spacer>
                        <v-btn color="blue darken-1" flat @click="close">Cancel</v-btn>
                        <v-btn color="blue darken-1" flat @click="save">Save</v-btn>
                    </v-card-actions>
                  </v-card>
              </v-dialog>
            </v-toolbar>
            <!-- DATA TABLE STARTS HERE -->
            <v-data-table
            :headers="headers"
            :items="categorias"
            :search="search"
            class="elevation-3"
            >
            <template slot="items" slot-scope="props">
                  <td class="text-xs-center">
                  <v-icon small class="mr-2" @click="editItem(props.item)">edit</v-icon>
                  <v-icon small @click="deleteItem(props.item)">delete</v-icon>
                  </td>
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
                <v-btn color="primary" @click="initialize">Resetear</v-btn>
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
          categorias:[],
          dialog: false,
          headers: [
            { text: 'Opciones', value: 'opciones', align : 'center' ,sortable: false },
            { text: 'Nombres', value: 'nombre' },
            { text: 'Descripción', value: 'descripcion',sorteable: false },
            { text: 'Estado', value: 'condicion',sorteable: false }
          ],
          search: '',
          editedIndex: -1,
          editedItem: {
            name: '',
            calories: 0,
            fat: 0,
            carbs: 0,
            protein: 0
          },
          defaultItem: {
            name: '',
            calories: 0,
            fat: 0,
            carbs: 0,
            protein: 0
          }
      }
    },
    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'Nueva Categoría' : 'Editar Categoría'
      }
    },

    watch: {
      dialog (val) {
        val || this.close()
      }
    },

    created () {
      this.listar();
    },

    methods: {
      listar(){
        let me = this;
        axios.get('api/Categorias/Listar').then(function(response){
          //console.log(response)
          me.categorias=response.data;
        }).catch(function(error){
          //console.log(error)
        })
      },

      editItem (item) {
        this.editedIndex = this.desserts.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialog = true
      },

      deleteItem (item) {
        const index = this.desserts.indexOf(item)
        confirm('Are you sure you want to delete this item?') && this.desserts.splice(index, 1)
      },

      close () {
        this.dialog = false
        setTimeout(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        }, 300)
      },

      save () {
        if (this.editedIndex > -1) {
          Object.assign(this.desserts[this.editedIndex], this.editedItem)
        } else {
          this.desserts.push(this.editedItem)
        }
        this.close()
      }
    }
  }
</script>
<style>
table th + th { border-left:1px solid #dddddd; }
table td + td { border-left:1px solid #dddddd; }
</style>