<template>
<v-card>
    
  <v-data-table :headers="headers" :items="pacientes" :search="search" class="elevation-1">
    <template v-slot:top>
      <v-toolbar flat color="#3797a4">
        <v-toolbar-title class="#0c354a--text"
          >Búsqueda de pacientes en el banco de las EPS e IPS</v-toolbar-title>
       
        <v-dialog v-model="dialog" max-width="800px">
          <v-card>
            <v-toolbar color="#3797a4">
              <v-card-title>
                Ficha informativa del paciente
                <v-spacer>
                  <v-avatar color="#ee6f57">
                    <v-icon dark size="40"> mdi-account </v-icon>
                  </v-avatar>
                </v-spacer>
              </v-card-title>
            </v-toolbar>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field
                      v-model="CamposDetalle.id"
                      label="Cedula Paciente"
                      readonly
                    >
                    </v-text-field>
                  </v-col>
                

                  <v-col cols="12" sm="8" md="4">
                    <v-text-field
                      v-model="CamposDetalle.nombre"
                      label="Nombre"
                      readonly
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field
                      v-model="CamposDetalle.apellidos"
                      label="Apellidos"
                      readonly
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field
                      v-model="CamposDetalle.fecha_nac"
                      label="Fecha de Nacimiento"
                      readonly
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field
                      v-model="CamposDetalle.edad"
                      label="Edad"
                      readonly
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field
                      v-model="CamposDetalle.sexo"
                      label="Sexo"
                      readonly
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field
                      v-model="CamposDetalle.ocupacion"
                      label="Ocupación"
                      readonly
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field
                      v-model="CamposDetalle.estado_civil"
                      label="Estado Civil"
                      readonly
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="close"> Cerrar </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
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
      <v-icon  class="mr-2" @click="VerDetalle(item)">
        mdi-account
      </v-icon>
      <v-icon  @click="ExamenesPaciente(item)"> mdi-needle </v-icon>
    </template>
    
  </v-data-table>
  </v-card>
</template>

<script>
const url_api = "http://localhost:3001/pacientes/";
export default {
  layout: "auxiliar",
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
      { text: "Edad", value: "edad" },
      { text: "Operaciones", value: "actions" },
    ],
    pacientes: [],
    search: '',
    CamposDetalle: {
      nombre: "",
      apellidos: "",
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

  methods: {
    async getPacientes() {
      try {
        let response = await this.$axios.get(url_api);
        this.pacientes = response.data;
      } catch (error) {
        console.error(error);
      }
    },

    VerDetalle(item) {
      console.log(item);
      this.CamposDetalle = Object.assign({}, item);
      this.dialog = true;
    },

    ExamenesPaciente(item) {
      localStorage.setItem("user-detalle", JSON.stringify(item));
      this.$router.push("auxiliarListaExamenes");
    },

    close() {
      this.dialog = false;
    },
  },
};
</script>

<style>
</style>