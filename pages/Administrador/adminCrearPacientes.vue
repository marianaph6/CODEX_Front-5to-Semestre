<template >
  <v-card
    style="width: 600px"
    elevation="12"
    row
    wrap
    align-center
    justify-center
  >
    <v-toolbar color="#3797a4">
      <v-card-title class="#0c354a--text">
        <center>Paciente</center>
      </v-card-title>
      <v-spacer></v-spacer>
    </v-toolbar>

    <v-card-text>
      <v-form ref="formPaciente" v-model="valid" lazy-validation>
        <v-row>
          <v-col cols="12" sm="6">
            <v-text-field
              v-model="paciente.nombre"
              label="Nombre"
              :rules="rules.required"
              required
            ></v-text-field>
          </v-col>
          <v-col cols="12" sm="6">
            <v-text-field
              v-model="paciente.apellidos"
              label="Apellidos"
              :rules="rules.required"
              required
            ></v-text-field>
          </v-col>
        </v-row>
        <v-text-field
          v-model="paciente.id"
          label="Cédula"
          :rules="rules.required"
          required
        ></v-text-field>
        <v-row>
          <v-col cols="12" sm="4">
            <v-menu
              v-model="menu2"
              :close-on-content-click="false"
              :nudge-right="40"
              transition="scale-transition"
              offset-y
              min-width="auto"
            >
              <template v-slot:activator="{ on, attrs }">
                <v-text-field
                  v-model="paciente.fecha_nac"
                  label="Fecha de Nacimiento"
                  prepend-icon="mdi-calendar"
                  readonly
                  v-bind="attrs"
                  v-on="on"
                ></v-text-field>
              </template>
              <v-date-picker
                v-model="paciente.fecha_nac"
                @input="menu2 = false"
              ></v-date-picker>
            </v-menu>
          </v-col>
          <v-col cols="12" sm="4">
            <v-text-field
              v-model="paciente.edad"
              label="Edad"
              :rules="rules.required"
              required
            ></v-text-field>
          </v-col>
          <v-col cols="12" sm="4">
            <v-select
              v-model="paciente.sexo"
              :items="sexo"
              label="Sexo"
              :rules="rules.required"
              required
            ></v-select>
          </v-col>
        </v-row>
        <v-text-field
          v-model="paciente.ocupacion"
          label="Ocupación"
          :rules="rules.required"
          required
        ></v-text-field>
        <v-select
          :items="estado_civil"
          v-model="paciente.estado_civil"
          label="Estado civil"
          :rules="rules.required"
          required
        ></v-select>
        <v-text-field
          v-model="paciente.correo"
          label="Correo"
          :rules="emailRules"
          required
        ></v-text-field>
        <v-text-field
          v-model="paciente.telefono"
          label="Teléfono"
          :rules="rules.required"
          required
        ></v-text-field>
        <v-row>
          <v-col cols="12" sm="6">
            <v-select
              v-model="paciente.departamento"
              :items="departamentos"
              label="Departamento"
              :rules="rules.required"
              required
            ></v-select>
          </v-col>
          <v-col cols="12" sm="6">
            <v-select
              v-model="paciente.ciudad"
              :items="ciudades"
              label="Ciudad"
              :rules="rules.required"
              required
            ></v-select>
          </v-col>
        </v-row>
        <v-text-field
          v-model="paciente.direccion"
          label="Dirección"
          :rules="rules.required"
          required
        ></v-text-field>
        <v-btn class="white--text" color="#ee6f57" @click="GuardarPaciente()">
          Crear
        </v-btn>
      </v-form>
    </v-card-text>
  </v-card>
</template>

<script>
const url_apipaciente = "http://localhost:3001/pacientes/";
const url_apimedico = "http://localhost:3001/medicos/";
const url_apiauxiliar = "http://localhost:3001/auxiliares/";
export default {
  layout: "admin",
  data: () => ({
    date: new Date().toISOString().substr(0, 10),
    menu2: false,
    valid: true,
    validar: false,
    sexo: ["Masculino", "Femenino"],
    estado_civil: ["Soltero", "Casado", "Divorciado", "Viudo"],
    departamentos: ["Antioquia", "Arauca", "Atlántico", "Bolívar"],
    ciudades: ["Medellín", "Bogotá", "Barranquilla", "Cartagena"],

    paciente: {
      nombre: "",
      apellidos: "",
      id: "",
      fecha_nac: new Date().toISOString().substr(0, 10),
      edad: "",
      sexo: "",
      ocupacion: "",
      estado_civil: "",
      correo: "",
      telefono: "",
      departamento: "",
      ciudad: "",
      direccion: "",
      perfil: "Paciente",
    },

    pac: [],
    med: [],
    aux: [],
    usu: [],

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

    async GuardarPaciente() {
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
          this.usu.push((JSON.stringify(i.id)));
        }
      } catch (error) {
        console.error(error);
      }

      for (var i of this.usu) {
        if(i==JSON.stringify(this.paciente.id)){
          this.validar=true;
        }
      }
      
      console.log(this.validar);
       if (this.$refs.formPaciente.validate()) {
        if (this.validar == false) {
          this.validar = false;
          try {
            let paciente = Object.assign({}, this.paciente);
            let response = await this.$axios.post(url_apipaciente, paciente);
            this.$swal.fire({
              type: "success",
              title: "Operación exitosa.",
              text: "El paciente se guardó correctamente.",
            });
            this.paciente = "";
            this.$router.push("adminUsuarios");
          } catch (error) {
            console.log(error);
          }
        } else {
          this.$swal.fire({
            type: "warning",
            title: "Formulario incorrecto.",
            text: "Hay campos mal diligenciados .",
          });
        }

        // Crear un nuevo objeto con la info del usuario
      } else {
        this.$swal.fire({
          type: "warning",
          title: "Formulario incompleto.",
          text: "Hay campos que deben ser diligenciados.",
        });
      }  
    },
  },
};
</script>

<style>
</style>