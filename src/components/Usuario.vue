<template>
  <v-layout align-start>
      <v-flex>
            <v-toolbar flat color="#b2dfdb">
              <v-toolbar-title>Usuarios</v-toolbar-title>
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
                              <v-flex xs12 sm6 md6>
                                <v-text-field v-model="nombre" label="Nombre">
                                </v-text-field>
                              </v-flex>
                              <!-- SLOT ROL SELECT-->
                              <v-flex xs12 sm6 md6>
                                <v-select v-model="idrol"
                                :items="roles" label="Rol">
                                </v-select>
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
                              <v-flex xs12 sm6 md6>
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
                              <!-- SLOT PASSWORD -->
                              <v-flex xs12 sm6 md6>
                                <v-text-field type="password" v-model="password" label="Password"></v-text-field>
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
              <!-- AD MODAL STARTS HERE ---->
              <v-dialog v-model="adModal" max-width="290">
                  <v-card>
                      <v-card-title class="justify-center" v-if="adAccion==1">
                        <span class="headline">¿Activar Usuario?</span>
                      </v-card-title>
                      <v-card-title class="justify-center" v-if="adAccion==2">
                        <span class="headline">¿Desactivar Usuario?</span>
                      </v-card-title>
                      <v-card-text>
                           Estas a punto de 
                           <span v-if="adAccion==1">Activar</span>
                           <span v-if="adAccion==2">Desactivar</span>
                           al usuario {{adNombre}}                           
                      </v-card-text>
                      <v-card-actions>
                        <v-spacer></v-spacer>
                          <v-btn v-if="adAccion" color="green darken-1" flat="flat" @click="activarDesactivarCerrar">Cancelar</v-btn>
                          <v-btn v-if="adAccion==1" color="orange darken-1" flat="flat" @click="activar">Activar</v-btn>
                          <v-btn v-if="adAccion==2" color="orange darken-1" flat="flat" @click="desactivar">Desactivar</v-btn>
                        <v-spacer></v-spacer>
                    </v-card-actions>
                  </v-card>
              </v-dialog>
            </v-toolbar>
            <!-- DATA TABLE STARTS HERE -->
            <v-data-table
            :headers="headers"
            :items="usuarios"
            :search="search"
            class="elevation-3"
            >
            <template slot="items" slot-scope="props">
                  <td class="text-xs-center">
                    <v-icon small class="mr-2" @click="editItem(props.item)">edit</v-icon>
                    <template v-if="props.item.condicion">
                      <v-icon small @click="activarDesactivarMostrar(2,props.item)">block</v-icon>
                    </template>
                    <template v-else>
                      <v-icon small @click="activarDesactivarMostrar(1,props.item)">check</v-icon>
                    </template>
                  </td>
                  <td class="text-xs-left">{{ props.item.nombre }}</td>
                  <td class="text-xs-left">{{ props.item.rol }}</td>
                  <td class="text-xs-left">{{ props.item.tipo_documento }}</td>
                  <td class="text-xs-left">{{ props.item.num_documento }}</td>
                  <td class="text-xs-left">{{ props.item.direccion }}</td>
                  <td class="text-xs-left">{{ props.item.telefono }}</td>
                  <td class="text-xs-left">{{ props.item.email }}</td>
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
          usuarios:[],
          dialog: false,
          headers: [
            { text: 'Opciones', value: 'opciones',sortable: false , align : 'center'},
            { text: 'Nombre', value: 'nombre', align : 'center'},
            { text: 'Rol', value: 'rol', align : 'center'},
            { text: 'Tipo Documento', value: 'tipo_documento', align : 'center'},
            { text: 'Num. Documento', value: 'num_documento', align : 'center',sortable: false},
            { text: 'Dirección', value: 'direccion', align : 'center',sortable: false},   
            { text: 'Teléfono', value: 'telefono', align : 'center', sortable : false},                       
            { text: 'Email', value: 'email',sortable: false },
            { text: 'Estado', value: 'condicion',sortable: false },
          ],
          search: '',
          editedIndex: -1,
          id: '',
          idrol : '', 
          roles : [],
          nombre: '',
          tipo_documento: '',
          documentos: ['DNI','RUC','PASAPORTE','CEDULA'],
          num_documento : '',
          direccion: '',
          telefono: '',
          email: '',
          password: '',
          actPassword: false,
          passwordAnt:'',
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
        return this.editedIndex === -1 ? 'Nuevo Usuario' : 'Actualizar Usuario'
      }
    },

    watch: {
      dialog (val) {
        val || this.close()
      }
    },

    created () {
      this.listar();
      this.select();
    },

    methods: {
      listar(){
        let me = this;
        axios.get('api/Usuarios/Listar').then(function(response){
          //console.log(response)
          me.usuarios=response.data;
        }).catch(function(error){
          //console.log(error)
        })
      },

      select(){
        let me = this;
        var RolesArray = [] ;
        axios.get('api/Roles/Select').then(function(response){
          //console.log(response)
          RolesArray=response.data;
          RolesArray.map(function(x){
              me.roles.push({text: x.nombre,value: x.idrol});
          });
        }).catch(function(error){
          //console.log(error)
        })
      },

      editItem (item) {
        this.id=item.idusuario;
        this.idrol=item.idrol;
        this.nombre=item.nombre;
        this.tipo_documento=item.tipo_documento;
        this.num_documento=item.num_documento;
        this.direccion=item.direccion;
        this.telefono=item.telefono;
        this.email=item.email;
        this.password=item.password_hash;
        this.passwordAnt=item.password_hash;
        this.editedIndex=1;
        this.dialog = true
      },

      close () {
        this.dialog = false
        this.limpiar();
      },

      limpiar(){
          this.id="";
          this.idrol="";
          this.nombre="";
          this.tipo_documento="";
          this.num_documento="";
          this.direccion="";
          this.telefono="";
          this.email="";
          this.password="";
          this.passwordAnt="";
          this.actPassword= false;
          this.editedIndex= -1;
      },

      guardar () {
        if(this.validar()){   // validar() = 1
          return
        }
        if (this.editedIndex > -1) {
          //Code to edit
          let me = this;

          if(me.password!=me.passwordAnt){
             me.actPassword=true;
          }

          axios.put('api/Usuarios/Actualizar',{
            'idusuario': me.id,
            'idrol': me.idrol,
            'nombre': me.nombre,
            'tipo_documento': me.tipo_documento,
            'num_documento': me.num_documento,
            'direccion': me.direccion,
            'telefono': me.telefono,
            'email': me.email,
            'password': me.password,
            'act_password': me.actPassword,
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
          axios.post('api/Usuarios/Crear',{
            'idrol': me.idrol,
            'nombre': me.nombre,
            'tipo_documento': me.tipo_documento,
            'num_documento': me.num_documento,
            'direccion': me.direccion,
            'telefono': me.telefono,
            'email': me.email,
            'password': me.password,
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
          if(!this.idrol){
              this.validamensaje.push("Sekeccione un rol para el usuario.")
          }
          if(!this.tipo_documento){
              this.validamensaje.push("Seleccione un tipo de documento.")
          }
          if(!this.num_documento){
              this.validamensaje.push("Ingrese un número de documento.")
          }
          if(!this.email){
              this.validamensaje.push("Ingrese el email del usuario.")
          }
          if(!this.password){
              this.validamensaje.push("Ingrese el password del usuario.")
          }
          if(this.validamensaje.length){
              this.valida=1;
          }
          return this.valida;
      },

      activarDesactivarMostrar(accion,item){
          this.adModal = true;
          this.adNombre = item.nombre;
          this.adId = item.idusuario;

          if(accion==1){
            this.adAccion = 1;
          }
          else if(accion==2){
            this.adAccion = 2;           
          }
          else{
              this.adModal= false;
          }
      },
      
      activarDesactivarCerrar(){
        this.adModal=false;
      },

      activar(){
          let me = this;
            axios.put('api/Usuarios/Activar/'+this.adId,{}).then(function(response){
                me.adModal=false,
                me.adAccion = 0,
                me.adId = "",
                me.adNombre = "";
                me.listar();
              }).catch(function(error){
                console.log(error);
              })
      },

      desactivar(){
          let me = this;
            axios.put('api/Usuarios/Desactivar/'+this.adId,{}).then(function(response){
                me.adModal=false,
                me.adAccion = 0,
                me.adId='',
                me.adNombre = '';
                me.listar();
              }).catch(function(error){
                console.log(error);
              })
      }     
    }
  }
</script>
<style>
table th + th { border-left:1px solid #dddddd; }
table td + td { border-left:1px solid #dddddd; }
</style>