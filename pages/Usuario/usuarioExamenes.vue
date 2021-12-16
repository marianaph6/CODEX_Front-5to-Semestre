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
   
    </v-card-title>
    </template>
    
    
  </v-data-table>
  </v-card>
</template>

<script>
const url_apiexamen = "http://localhost:3001/examenes/";

export default {
  layout: "usuario",
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
      let stringUser2=localStorage.getItem("user-paciente");
      this.paciente = JSON.parse(stringUser2);
    },

    validate() {
      this.$refs.form.validate();
    },

    async getExamen() {
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


  },
};
</script>

<style>
</style>