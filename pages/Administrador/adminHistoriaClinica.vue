<template>
  <v-card>
    <v-data-table
      :headers="headers"
      :items="pacientes"
      :search="search"
      class="elevation-1"
    >
      <template v-slot:top>
        <v-toolbar flat color="#3797a4">
          <v-toolbar-title class="#0c354a--text">
            Base de datos pacientes
            <v-divider class="mx-2" inset vertical> </v-divider>
            <v-spacer></v-spacer>
          </v-toolbar-title>

        
        </v-toolbar>

      </template>
      <template v-slot:item.actions="{ item }">
        <v-icon class="mr-2" @click="verHistoria(item)"> mdi-file-document-multiple-outline </v-icon>
      </template>
    </v-data-table>
  </v-card>
</template>

<script>
const url_apipaciente = "http://localhost:3001/pacientes/";
const url_apimedico = "http://localhost:3001/medicos/";
const url_apiauxiliar = "http://localhost:3001/auxiliares/";
export default {
  layout: "admin",
  beforeMount() {
    localStorage.setItem("user-detalle", "");
    this.getPacientes();
  },
  data: () => ({
    dialog: false,

    headers: [
      {
        text: "Cedula",
        align: "start",
        value: "id",
      },
      { text: "Nombre", value: "nombre" },
      { text: "Apellidos", value: "apellidos" },
      { text: "Perfil", value: "perfil" },
      { text: "Operaciones", value: "actions" },
    ],
    pacientes: [],
    usuarios: [],
    search: "",
    CamposDetalle: {
      nombre: "",
      apellidos: "",
      cedula: "",
      fecha_nac: "",
      edad: "",
      sexo: "",
      ocupacion: "",
      estado_civil: "",
      correo: "",
      telefono: "",
      departamento: "",
      ciudad: "",
      direccion: "",
      id: 0,
    },
  }),

  computed: {
    
  },

  methods: {
    
    async getPacientes() {
      try {
        let response = await this.$axios.get(url_apipaciente);
        this.pacientes = response.data;
        this.usuarios = response.data;
      } catch (error) {
        console.error(error);
      }
    },
    

    verHistoria(item) {
      localStorage.setItem("user-historia-admin", JSON.stringify(item));
      this.$router.push("adminDetalleHistoria");
    },

    

    close() {
      this.dialog = false;
    },


    
  },
};
</script>

<style>
</style>

