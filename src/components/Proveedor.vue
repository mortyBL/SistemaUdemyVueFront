<template>
  <v-layout align-start>
      <v-flex>
            <v-toolbar flat color="#b2dfdb">
              <v-toolbar-title>Proveedores</v-toolbar-title>
              <v-divider
                  class="mx-2"
                  inset
                  vertical
              ></v-divider>
              <v-spacer></v-spacer>
              <v-text-field class="text-xs-center" v-model="search" append-icon="search" label="Búsqueda" single-line hide-details></v-text-field>
              <v-spacer></v-spacer>
              <!-- DIALOG STARTS HERE -->
              <v-dialog v-model="dialog" max-width="500px">
                  <v-btn slot="activator" color="primary" dark class="mb-2">Nuevo</v-btn>
                  <v-card>
                    <v-card-title class="justify-center">
                        <span class="headline">{{ formTitle }}</span>
                    </v-card-title>
                    <v-card-text>
                        <v-container grid-list-md>
                          <v-layout wrap>
                              <!--SLOT NOMBRE -->
                              <v-flex xs12 sm12 md12>
                                <v-text-field v-model="nombre" label="Nombre">
                                </v-text-field>
                              </v-flex>
                              <!-- SLOT TIPO DOCUMENTO SELECT -->
                              <v-flex xs12 sm6 md6>
                                 <v-select v-model="tipo_documento"
                                  :items="documentos" label="Tipo Documentos">
                                 </v-select>
                              </v-flex>
                              <!-- SLOT NUMERO DOCUMENTO  -->
                              <v-flex xs12 sm6 md6>
                                <v-text-field v-model="num_documento" label="Número Documento"></v-text-field>
                              </v-flex>
                              <!-- SLOT DIRECCIÓN -->
                              <v-flex xs12 sm12 md12>
                                <v-text-field v-model="direccion" label="Dirección"></v-text-field>
                              </v-flex>
                              <!-- SLOT EMAIL -->
                              <v-flex xs12 sm6 md6>
                                <v-text-field v-model="telefono" label="Teléfono"></v-text-field>
                              </v-flex>
                              <!-- SLOT EMAIL -->
                              <v-flex xs12 sm6 md6>
                                <v-text-field v-model="email" label="Email"></v-text-field>
                              </v-flex>
                              <!-- LABEL VALIDA -->
                              <v-flex xs12 sm12 md12 v-show="valida">
                                <div class="red--text" v-for="v in validamensaje" :key="v" v-text="v">
                                </div>
                              </v-flex>
                          </v-layout>
                        </v-container>
                    </v-card-text>

                    <v-card-actions>
                        <v-spacer></v-spacer>
                          <v-btn color="blue darken-1" flat @click="close">Cancelar</v-btn>
                          <v-btn color="blue darken-1" flat @click="guardar">Guardar</v-btn>
                        <v-spacer></v-spacer>
                    </v-card-actions>
                  </v-card>
              </v-dialog>
            </v-toolbar>
            <!-- DATA TABLE STARTS HERE -->
            <v-data-table
            :headers="headers"
            :items="proveedores"
            :search="search"
            class="elevation-3"
            >
            <template slot="items" slot-scope="props">
                  <td class="text-xs-center">
                    <v-icon small class="mr-2" @click="editItem(props.item)">edit</v-icon>
                  </td>
                  <td class="text-xs-left">{{ props.item.nombre }}</td>
                  <td class="text-xs-left">{{ props.item.tipo_persona }}</td>
                  <td class="text-xs-left">{{ props.item.tipo_documento }}</td>
                  <td class="text-xs-left">{{ props.item.num_documento }}</td>
                  <td class="text-xs-left">{{ props.item.direccion }}</td>
                  <td class="text-xs-left">{{ props.item.telefono }}</td>
                  <td class="text-xs-left">{{ props.item.email }}</td>
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
          proveedores:[],
          dialog: false,
          headers: [
            { text: 'Opciones', value: 'opciones',sortable: false , align : 'center'},
            { text: 'Nombre', value: 'nombre', align : 'center'},
            { text: 'Tipo Persona', value: 'tipo_persona', align : 'center'},
            { text: 'Tipo Documento', value: 'tipo_documento', align : 'center'},
            { text: 'Num. Documento', value: 'num_documento', align : 'center',sortable: false},
            { text: 'Dirección', value: 'direccion', align : 'center',sortable: false},   
            { text: 'Teléfono', value: 'telefono', align : 'center', sortable : false},                       
            { text: 'Email', value: 'email',sortable: false },
          ],
          search: '',
          editedIndex: -1,
          id: '',
          nombre: '',
          tipo_documento: '',
          documentos: ['DNI','RUC','PASAPORTE','CEDULA'],
          num_documento : '',
          direccion: '',
          telefono: '',
          email: '',
          valida: 0,
          validamensaje: [],
          adModal: false,
          adAccion: 0,
          adNombre : '',
          adId : ''
      }
    },
    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'Nuevo Proveedor' : 'Actualizar Proveedor'
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
        axios.get('api/Personas/ListarProveedores').then(function(response){
          //console.log(response)
          me.proveedores=response.data;
        }).catch(function(error){
          //console.log(error)
        })
      },

      editItem (item) {
        this.id=item.idpersona;
        this.nombre=item.nombre;
        this.tipo_documento=item.tipo_documento;
        this.num_documento=item.num_documento;
        this.direccion=item.direccion;
        this.telefono=item.telefono;
        this.email=item.email;
        this.editedIndex=1;
        this.dialog = true
      },

      close () {
        this.dialog = false
        this.limpiar();
      },

      limpiar(){
          this.id="";
          this.nombre="";
          this.tipo_documento="";
          this.num_documento="";
          this.direccion="";
          this.telefono="";
          this.email="";
          this.editedIndex= -1;
      },

      guardar () {
        if(this.validar()){   // validar() = 1
          return
        }
        if (this.editedIndex > -1) {
          //Code to edit
          let me = this;

          axios.put('api/Personas/Actualizar',{
            'idpersona': me.id,
            'tipo_persona': 'Proveedor',
            'nombre': me.nombre,
            'tipo_documento': me.tipo_documento,
            'num_documento': me.num_documento,
            'direccion': me.direccion,
            'telefono': me.telefono,
            'email': me.email,
            }).then(function(response){
              me.close();
              me.listar();
              me.limpiar();
            }).catch(function(error){
              console.log(error);
            })
          
        } else {
          //Code to save
          let me = this;
          axios.post('api/Personas/Crear',{
            'tipo_persona': 'Proveedor',
            'nombre': me.nombre,
            'tipo_documento': me.tipo_documento,
            'num_documento': me.num_documento,
            'direccion': me.direccion,
            'telefono': me.telefono,
            'email': me.email,
            }).then(function(response){
              me.close();
              me.listar();
              me.limpiar();
            }).catch(function(error){
              console.log(error);
            })
        }
      },

      validar() {
          this.valida = 0;
          this.validamensaje = [];

          if(this.nombre.length < 3 || this.nombre.lenght > 100){
              this.validamensaje.push("El nombre debe de tener más de 3 caracteres y menos de 100 caracteres.")
          }
          if(!this.tipo_documento){
              this.validamensaje.push("Seleccione un tipo de documento.")
          }
          if(!this.num_documento){
              this.validamensaje.push("Ingrese un número de documento.")
          }
          if(this.validamensaje.length){
              this.valida=1;
          }
          return this.valida;
      },
    
    }
  }
</script>
<style>
table th + th { border-left:1px solid #dddddd; }
table td + td { border-left:1px solid #dddddd; }
</style>