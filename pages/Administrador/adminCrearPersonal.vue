<template>
  <v-card style="width: 550px" elevation="12">
    <v-toolbar color="#3797a4">
      <v-card-title class="#0c354a--text">
        <center>Auxiliar - Medico</center>
      </v-card-title>
    </v-toolbar>
    <v-card-text>
      <v-form ref="formPersonalMedico" v-model="valid" lazy-validation>
        <v-row>
          <v-col cols="12" sm="6">
            <v-text-field
              v-model="personal.nombre"
              label="Nombre"
              :rules="rules.required"
              required
            ></v-text-field>
          </v-col>
          <v-col cols="12" sm="6">
            <v-text-field
              v-model="personal.apellidos"
              label="Apellidos"
              :rules="rules.required"
              required
            ></v-text-field>
          </v-col>
        </v-row>
        <v-text-field
          v-model="personal.id"
          label="Cédula"
          :rules="rules.required"
          required
        ></v-text-field>
        <v-row>
          <v-col cols="12" sm="4">
            <v-menu
              v-model="menu1"
              :close-on-content-click="false"
              :nudge-right="40"
              transition="scale-transition"
              offset-y
              min-width="auto"
            >
              <template v-slot:activator="{ on, attrs }">
                <v-text-field
                  v-model="personal.fecha_nac"
                  label="Fecha de Nacimiento"
                  prepend-icon="mdi-calendar"
                  readonly
                  v-bind="attrs"
                  v-on="on"
                ></v-text-field>
              </template>
              <v-date-picker
                v-model="personal.fecha_nac"
                @input="menu1 = false"
              ></v-date-picker>
            </v-menu>
          </v-col>
          <v-col cols="12" sm="4">
            <v-text-field
              v-model="personal.edad"
              label="Edad"
              :rules="rules.required"
              required
            ></v-text-field>
          </v-col>
          <v-col cols="12" sm="4">
            <v-select
              v-model="personal.sexo"
              :items="sexo"
              label="Sexo"
              :rules="rules.required"
              required
            ></v-select>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12" sm="6">
            <v-select
              :items="especialidad"
              v-model="personal.especialidad"
              label="Especialidad"
              :rules="rules.required"
              required
            ></v-select>
          </v-col>
          <v-col cols="12" sm="6">
            <v-select
              :items="perfil"
              v-model="personal.perfil"
              label="Peril"
              :rules="rules.required"
              required
            ></v-select>
          </v-col>
        </v-row>

        <v-select
          :items="estado_civil"
          v-model="personal.estado_civil"
          label="Estado civil"
          :rules="rules.required"
          required
        ></v-select>
        <v-text-field
          v-model="personal.correo"
          label="Correo"
          :rules="emailRules"
          required
        ></v-text-field>
        <v-text-field
          v-model="personal.telefono"
          label="Teléfono"
          :rules="rules.required"
          required
        ></v-text-field>
        <v-row>
          <v-col cols="12" sm="6">
            <v-select
              v-model="personal.departamento"
              :items="departamentos"
              label="Departamento"
              :rules="rules.required"
              required
            ></v-select>
          </v-col>
          <v-col cols="12" sm="6">
            <v-select
              v-model="personal.ciudad"
              :items="ciudades"
              label="Ciudad"
              :rules="rules.required"
              required
            ></v-select>
          </v-col>
        </v-row>
        <v-text-field
          v-model="personal.direccion"
          label="Dirección"
          :rules="rules.required"
          required
        ></v-text-field>
        <v-btn class="white--text" color="#ee6f57" @click="GuardarPersonal()">
          Crear
        </v-btn>
      </v-form>
    </v-card-text>
  </v-card>
</template>

<script>
const url_apimedico = "http://localhost:3001/medicos/";
const url_apiauxiliar = "http://localhost:3001/auxiliares/";
const url_apiesp = "http://localhost:3001/especialidades/";
const url_apipaciente = "http://localhost:3001/pacientes/";
export default {
  beforeMount() {
    this.getEspecialidad();
  },
  layout: "admin",
  data: () => ({
    date: new Date().toISOString().substr(0, 10),
    menu1: false,
    valid: true,
    validar: false,
    sexo: ["Masculino", "Femenino"],
    estado_civil: ["Soltero", "Casado", "Divorciado", "Viudo"],
    departamentos: ["Antioquia", "Arauca", "Atlántico", "Bolívar"],
    ciudades: ["Medellín", "Bogotá", "Barranquilla", "Cartagena"],
    especialidad: [],
    esp: [],
    perfil: ["Medico", "Auxiliar"],
        pac: [],
    med: [],
    aux: [],
    usu: [],

    personal: {
      nombre: "",
      apellidos: "",
      id: "",
      fecha_nac: new Date().toISOString().substr(0, 10),
      edad: "",
      sexo: "",
      perfil: "",
      especialidad: "",
      estado_civil: "",
      correo: "",
      telefono: "",
      departamento: "",
      ciudad: "",
      direccion: "",
    },
    rules: {
      required: [(v) => !!v || "El campo es obligatorio"],
    },
    emailRules: [
      (v) => !!v || "El correo es obligatorio",
      (v) => /.+@.+\..+/.test(v) || "El correo no es valido",
    ],

    search: "",
  }),

  methods: {
    validate() {
      this.$refs.form.validate();
    },

    async getEspecialidad() {
      try {
        let response = await this.$axios.get(url_apiesp);
        this.esp = response.data;
        for (var i of this.esp) {
          this.especialidad.push(i.nombre);
        }
      } catch (error) {
        console.error(error);
      }
    },

    async GuardarPersonal() {
      this.validar = false;

      try {
        let response1 = await this.$axios.get(url_apipaciente);
        this.pac = response1.data;
        for (var i of this.pac) {
          this.usu.push(JSON.stringify(i.id));
        }
      } catch (error) {
        console.error(error);
      }

      try {
        let response2 = await this.$axios.get(url_apimedico);
        this.med = response2.data;
        for (var i of this.med) {
          this.usu.push(JSON.stringify(i.id));
        }
      } catch (error) {
        console.error(error);
      }

      try {
        let response3 = await this.$axios.get(url_apiauxiliar);
        this.aux = response3.data;
        for (var i of this.aux) {
          this.usu.push(JSON.stringify(i.id));
        }
      } catch (error) {
        console.error(error);
      }

      for (var i of this.usu) {
        if (i == JSON.stringify(this.personal.id)) {
          this.validar = true;
        }
      }

      console.log(this.validar);
      if (this.$refs.formPersonalMedico.validate()) {
        if (this.validar == false) {
          this.validar = false;
          if (this.personal.perfil == "Medico") {
            try {
              let medico = Object.assign({}, this.personal);
              let response = await this.$axios.post(url_apimedico, medico);
              this.$swal.fire({
                type: "success",
                title: "Operación exitosa.",
                text: "El medico se guardó correctamente.",
              });
              this.personal = "";
              this.$router.push("adminUsuarios");
            } catch (error) {
              console.log(error);
            }
          } else if (this.personal.perfil == "Auxiliar") {
            try {
              let auxiliar = Object.assign({}, this.personal);
              let response = await this.$axios.post(url_apiauxiliar, auxiliar);
              this.$swal.fire({
                type: "success",
                title: "Operación exitosa.",
                text: "El auxiliar se guardó correctamente.",
              });
              this.personal = "";
              this.$router.push("adminUsuarios");
              
            } catch (error) {
              console.log(error);
            }
          }
        } else {
          this.$swal.fire({
            type: "warning",
            title: "Formulario incorrecto.",
            text: "Hay campos mal diligenciados .",
          });
        }
      } else {
        this.$swal.fire({
          type: "warning",
          title: "Formulario incompleto.",
          text: "Hay campos que deben ser diligenciados.",
        });
      }
    },

    DeletePaciente() {
      //Enviar una solicitud (Request) en un metodo delete
    },

    BuscarPaciente() {
      //Enviar una solicitud (Request) en un metodo get
    },

    ActualizarPaciente() {
      //Enviar una solicitud (Request) en un metodo update
    },
  },
};
</script>

<style>
</style>