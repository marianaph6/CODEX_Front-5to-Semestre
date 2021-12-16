<template>
<v-card>
    
  <v-data-table :headers="headers" :items="examenpac" :search="search" class="elevation-1">
    <template v-slot:top>
      <v-toolbar flat color="#3797a4">
        <v-toolbar-title class="#0c354a--text"
          >Lista de examenes</v-toolbar-title>
       
       
      </v-toolbar>
       <v-card-title>
         
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="Buscar"
        single-line
        hide-details
      ></v-text-field>
       <v-spacer></v-spacer>
      <v-spacer>
        <div>
          <v-row justify="center">
            <v-dialog v-model="dialog" persistent max-width="600px">
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  justify="center"
                  class="white--text"
                  color="#ee6f57"
                  fab
                  small
                  dark
                  v-bind="attrs"
                  v-on="on"
                >
                  <v-icon> mdi-clipboard-plus-outline </v-icon>
                </v-btn>
              </template>
              <v-card>
                <v-toolbar color="#3797a4">
                  <v-card-title>
                    <span class="headline">Añadir nuevo examen</span>
                  </v-card-title>
                </v-toolbar>
                <v-card-text>
                  <v-form ref="formExamen" v-model="valid" lazy-validation>
                    <v-container>
                      <v-row>
                        <v-col cols="12" sm="8" md="6">
                          <v-text-field
                            label="Numero de Identificación (Paciente)"
                            required
                            readonly
                            v-model="paciente.id"
                            
                          
                            :rules="rules.required"
                            
                          ></v-text-field>
                        </v-col>
                        <v-col cols="12" sm="8" md="6">
                          <v-text-field
                            label="Numero de Identificación (Medico/Auxiliar)"
                            required
                            readonly
                            
                            v-model="auxiliar.id"
                            :rules="rules.required"
                          ></v-text-field>
                        </v-col>

                        <v-col cols="12">
                          <v-select
                            :items="area"
                            label="Area de laboratorio"
                            :rules="rules.required"
                            v-model="CamposExamen.arealab"
                            required
                          ></v-select>
                        </v-col>
                        <v-col cols="12">
                          <v-text-field
                            label="Nombre del examen"
                            required
                            :rules="rules.required"
                            v-model="CamposExamen.nombre"
                          ></v-text-field>
                        </v-col>
                        <v-col cols="12">
                          <v-file-input
                          :rules="rules.required"
                            accept="image/*"
                            label="Archivo de la orden"
                          ></v-file-input>
                        </v-col>
                      </v-row>
                    </v-container>
                  </v-form>
                </v-card-text>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue darken-1" text @click="dialog = false">
                    Cerrar
                  </v-btn>
                  <v-btn color="blue darken-1" text @click="GuardarExamen()">
                    Guardar
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </v-row>
        </div>
      </v-spacer>
    </v-card-title>
    </template>
    
    
  </v-data-table>
  </v-card>
</template>

<script>
const url_apiexamen = "http://localhost:3001/examenes/";

export default {
  layout: "auxiliar",
  beforeMount() {
    this.loadUser();
    this.getExamen();
  },
  data: () => ({
    dialog: false,
    auxiliar:{},
      paciente:{},
      rules: {
      required: [(v) => !!v || "El campo es obligatorio"],
    },
    
      search: "",
      area:['Hematología y coagulación','Químicas','Orina y heces','Inmunología','Serología','	Microbiología','Citometría de flujo y biología molecular'],
     

    headers: [
      {
        text: "Id",
        align: "start",
        value: "id",
      },
      
      { text: "Nombre", value: "nombre" },
      { text: "Area", value: "arealab" },
      { text: "Fecha", value: "fecha" },
    ],

    examenpac:[],
    search: '',
    examen: [],
    
    CamposExamen: {
      idpaciente:'',
        idauxiliar:'',
        arealab:'',
        nombre:'',
        fecha:'',
    },
  }),

  methods: {

        loadUser() {
      let stringUser=localStorage.getItem("user-auxiliar");
      this.auxiliar = JSON.parse(stringUser);
      let stringUser2=localStorage.getItem("user-detalle");
      this.paciente = JSON.parse(stringUser2);
    },

    validate() {
      this.$refs.form.validate();
    },

    async getExamen() {
    this.examenpac=[];
      try {
        let response = await this.$axios.get(url_apiexamen);
        this.examen = response.data;
        for(var i of this.examen){
          if(this.paciente.id==i.idpaciente){
            this.examenpac.push(i);
          }
        }

      } catch (error) {
        console.error(error);
      }
    },

  

    async GuardarExamen() {
      
      
      if (this.$refs.formExamen.validate()) {
      this.CamposExamen.idpaciente=this.paciente.id;
      this.CamposExamen.idauxiliar=this.auxiliar.id;
      this.CamposExamen.fecha=new Date().getDay()+"/"+new Date().getMonth()+"/"+new Date().getFullYear();
        // Crear un nuevo objeto con la info del usuario
        try {
          let examen = Object.assign({}, this.CamposExamen);
          let response = await this.$axios.post(url_apiexamen, examen);
          this.$swal.fire({
            type: "success",
            title: "Operación exitosa.",
            text: "El examen se guardó correctamente.",
          });
        } catch (error) {
          console.log(error);
        }
        
        this.dialog = false;
        this.getExamen();
      } else {
        this.$swal.fire({
          type: "warning",
          title: "Formulario incompleto.",
          text: "Hay campos que deben ser diligenciados.",
        });
      }
      
    },



    close() {
      this.dialog = false;
    },
  },
};
</script>

<style>
</style>