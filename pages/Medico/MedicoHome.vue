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
          <v-toolbar-title class="#0c354a--text"
            >Búsqueda de pacientes en el banco de las EPS e IPS</v-toolbar-title
          >
        </v-toolbar>
        <v-card-title>
          <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            label="Buscar"
            single-line
            hide-details
          ></v-text-field>
        </v-card-title>
      </template>
      <template v-slot:item.actions="{ item }">
        <v-btn
          fab
          dark
          color="inverted"
          small
          class="ml-1"
          @click="verHistorias_paciente(item)"
        >
          <v-icon>mdi-eye-plus</v-icon>
        </v-btn>
      </template>
    </v-data-table>
  </v-card>
</template>

<script>
const url_api = "http://localhost:3001/pacientes/";
export default {
  layout: "medico",
  beforeMount() {
    localStorage.setItem("user-detalle", "");
    this.getPacientes();
  },
  data: () => ({
    dialog: false,

    headers: [
      {
        text: "Id",
        align: "start",
        value: "id"
      },
      { text: "Cedula", value: "cedula" },
      { text: "Nombre", value: "nombre" },
      { text: "Apellidos", value: "apellidos" },
      { text: "Edad", value: "edad" },
      { text: "Operaciones", value: "actions" }
    ],
    pacientes: [],
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
      id: 0
    }
  }),

  methods: {
    async getPacientes() {
      try {
        let response = await this.$axios.get(url_api);
        this.pacientes = response.data;
      } catch (error) {
        console.error(error);
      }
    },
    verHistorias_paciente(item) {
      localStorage.setItem("estres", JSON.stringify(item));
      console.log(item.cedula);
      this.$router.push("Historia_clinica");
    },
    VerDetalle(item) {
      console.log(item);
      this.CamposDetalle = Object.assign({}, item);
      this.dialog = true;
    },
    close() {
      this.dialog = false;
    }
  }
};
</script>

<style></style>
