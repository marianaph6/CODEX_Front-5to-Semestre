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
            Lista de {{ TituloPerfil }}
            <v-divider class="mx-2" inset vertical> </v-divider>
            <v-spacer></v-spacer>
          </v-toolbar-title>

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
                        label="Id Paciente"
                        readonly
                      >
                      </v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="CamposDetalle.id"
                        label="Cedula Paciente"
                        readonly
                      ></v-text-field>
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
                <v-btn color="blue darken-1" text @click="close">
                  Cerrar
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
        <v-card-title>
          <v-btn
            justify="center"
            class="ma-2"
            color="#3797a4"
            fab
            small
            dark
            v-bind="attrs"
            v-on="on"
            @click="AgregarUsuario()"
          >
            <v-icon>mdi-account-plus </v-icon>
          </v-btn>
          <v-text-field
            v-model="search"
            class="ma-2"
            append-icon="mdi-magnify"
            label="Buscar"
            single-line
            hide-details
          ></v-text-field>
          <v-btn
            class="ma-2"
            small
            color="#ee6f57"
            v-on:click="Perfil('Medicos')"
            dark
          >
            <v-icon>mdi-doctor</v-icon>
          </v-btn>
          <v-btn
            class="ma-2"
            small
            color="#ee6f57"
            v-on:click="Perfil('Pacientes')"
            dark
          >
            <v-icon>mdi-account-heart</v-icon>
          </v-btn>
          <v-btn
            class="ma-2"
            small
            color="#ee6f57"
            v-on:click="Perfil('Auxiliares')"
            dark
          >
            <v-icon>mdi-hospital-building</v-icon>
          </v-btn>
        </v-card-title>
      </template>
      <template v-slot:item.actions="{ item }">
        <v-icon class="mr-2" @click="editItem(item)"> mdi-pencil </v-icon>
        <v-icon @click="deleteItem(item)"> mdi-delete </v-icon>
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
    this.getMedicos();
    this.getAuxiliares();
  },
  data: () => ({
    dialog: false,
    perfil: "Pacientes",

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
    medicos: [],
    auxiliares: [],
    usuarios: [],
    search: "",
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

  computed: {
    TituloPerfil() {
      if (this.perfil == "Pacientes") {
        return "Pacientes";
      } else if (this.perfil == "Medicos") {
        return "Medicos";
      } else if (this.perfil == "Auxiliares") {
        return "Auxiliares";
      }
    },
  },

  methods: {
    Perfil: function (dato) {
      if (dato == "Pacientes") {
        this.perfil = "Pacientes";
        this.pacientes = this.usuarios;
      } else if (dato == "Medicos") {
        this.perfil = "Medicos";
        this.pacientes = this.medicos;
      } else if (dato == "Auxiliares") {
        this.perfil = "Auxiliares";
        this.pacientes = this.auxiliares;
      }
    },
    async getPacientes() {
      try {
        let response = await this.$axios.get(url_apipaciente);
        this.pacientes = response.data;
        this.usuarios = response.data;
      } catch (error) {
        console.error(error);
      }
    },
    async getMedicos() {
      
      try {
        let response = await this.$axios.get(url_apimedico);
        this.medicos = response.data;
      } catch (error) {
        console.error(error);
      }
    },
    async getAuxiliares() {
     
      try {
        let response = await this.$axios.get(url_apiauxiliar);
        this.auxiliares = response.data;
      } catch (error) {
        console.error(error);
      }
    },

    VerDetalle(item) {
      console.log(item);
      this.CamposDetalle = Object.assign({}, item);
      this.dialog = true;
    },

    AgregarUsuario() {
      if (this.perfil == "Pacientes") {
        this.$router.push("adminCrearPacientes");
      } else if (this.perfil == "Medicos") {
        this.$router.push("adminCrearPersonal");
      } else if (this.perfil == "Auxiliares") {
        this.$router.push("adminCrearPersonal");
      }
    },

    close() {
      this.dialog = false;
    },

    editItem(item) {
      if (this.perfil == "Pacientes"){
        localStorage.setItem("pac-editar", JSON.stringify(item));
      this.$router.push('adminActualizarPaciente');
      }
      if (this.perfil == "Medicos"){
        localStorage.setItem("med-editar", JSON.stringify(item));
        localStorage.setItem("aux-editar", "");
      this.$router.push('adminActualizarPersonal');
      }
      if (this.perfil == "Auxiliares"){
        localStorage.setItem("aux-editar", JSON.stringify(item));
        localStorage.setItem("med-editar", "");
      this.$router.push('adminActualizarPersonal');
      }
      
    },

    deleteItem(item) {
      if (this.perfil == "Pacientes") {
        this.$swal
          .fire({
            type: "warning",
            title: "¿Está seguro de eliminar el perfil del paciente?",
            text: "Al borrar el perfil este no se prodrá recuperar.",
            allowEscapeKey: false,
            allowOutsideClick: false,
            showCancelButton: true,
          })
          .then(async (result) => {
            if (result.value) {
              try {
                let url = url_apipaciente + item.id;
                await this.$axios.delete(url);
                this.$swal.fire({
                  type: "success",
                  title: "Operación exitosa.",
                  text: "El perfil del paciente se eliminó correctamente.",
                });
                this.getPacientes();
              } catch (error) {
                this.$swal.fire({
                  type: "error",
                  title: "Ha ocurrido un problema al eliminar",
                  text: error.toString(),
                });
              }
            }
          });
      }
      if (this.perfil == "Medicos") {
        this.$swal
          .fire({
            type: "warning",
            title: "¿Está seguro de eliminar el perfil del medico?",
            text: "Al borrar el perfil este no se prodrá recuperar.",
            allowEscapeKey: false,
            allowOutsideClick: false,
            showCancelButton: true,
          })
          .then(async (result) => {
            if (result.value) {
              try {
                let url = url_apimedico + item.id;
                await this.$axios.delete(url);
                this.$swal.fire({
                  type: "success",
                  title: "Operación exitosa.",
                  text: "El perfil del medico se eliminó correctamente.",
                });
                this.getMedicos();
              } catch (error) {
                this.$swal.fire({
                  type: "error",
                  title: "Ha ocurrido un problema al eliminar",
                  text: error.toString(),
                });
              }
            }
          });
      }
      if (this.perfil == "Auxiliares") {
        this.$swal
          .fire({
            type: "warning",
            title: "¿Está seguro de eliminar el perfil del auxiliar?",
            text: "Al borrar el perfil este no se prodrá recuperar.",
            allowEscapeKey: false,
            allowOutsideClick: false,
            showCancelButton: true,
          })
          .then(async (result) => {
            if (result.value) {
              try {
                let url = url_apiauxiliar + item.id;
                await this.$axios.delete(url);
                this.$swal.fire({
                  type: "success",
                  title: "Operación exitosa.",
                  text: "El perfil del auxiliar se eliminó correctamente.",
                });
                this.getAuxiliares();
              } catch (error) {
                this.$swal.fire({
                  type: "error",
                  title: "Ha ocurrido un problema al eliminar",
                  text: error.toString(),
                });
              }
            }
          });
      }
    },
  },
};
</script>

<style>
</style>

