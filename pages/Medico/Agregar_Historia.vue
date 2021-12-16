<template>
  <div>
    <h1>Agregar entrada a historia clinica</h1>
    <template>
      <v-card-title>
        <v-spacer>
          <div>
            <v-row justify="bottom">
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
                  <h4>Agregar examen clinico</h4>
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
                              v-model="paciente.id"
                              required
                              readonly
                              :rules="rules.required"
                            ></v-text-field>
                          </v-col>
                          <v-col cols="12" sm="8" md="6">
                            <v-text-field
                              label="Numero de Identificación (Medico/Auxiliar)"
                              v-model="auxiliar.id"
                              required
                              readonly
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
      <v-card class="overflow-hidden">
        <center>
          <v-card-text>
            <v-form ref="formHistoria" v-model="valid" lazy-validation>
              <v-row>
                <v-menu
                  ref="menu"
                  v-model="menu"
                  :close-on-content-click="false"
                  :return-value.sync="historia.fecha"
                  transition="scale-transition"
                  offset-y
                  max-width="290px"
                  min-width="auto"
                >
                  <template v-slot:activator="{ on, attrs }">
                    <v-select
                      v-model="historia.especialidad"
                      :items="especialidad"
                      :rules="especialidadRules"
                      label="Especialidad"
                      required
                    ></v-select>

                    <v-text-field
                      v-model="historia.fecha"
                      solo-inverted
                      hide-details
                      label="Seleccionar fecha"
                      prepend-icon="mdi-calendar"
                      readonly
                      v-bind="attrs"
                      v-on="on"
                    ></v-text-field>
                  </template>
                  <v-date-picker v-model="historia.fecha" no-title scrollable>
                    <v-spacer></v-spacer>
                    <v-btn text color="primary" @click="menu = false">
                      Cancel
                    </v-btn>
                    <v-btn
                      text
                      color="primary"
                      @click="$refs.menu.save(historia.fecha)"
                    >
                      OK
                    </v-btn>
                  </v-date-picker>
                </v-menu>
              </v-row>

              <v-text-field
                v-model="historia.peso"
                :rules="pesoRules"
                label="Peso en (kg)"
                required
              ></v-text-field>

              <v-text-field
                v-model="historia.estatura"
                :rules="estaturaRules"
                label="Estatura en (mt)"
                required
              ></v-text-field>
              <h3>Motivo de la consulta</h3>
              <v-textarea
                v-model="historia.motivo_consulta"
                filled
                text-color="accent"
                name="input-7-4"
                label="Acá es donde deberá ingresar, la razon por la que el paciente viene."
              ></v-textarea>
              <h3>Descripcion</h3>
              <v-textarea
                v-model="historia.descripcion"
                filled
                name="input-7-4"
                label="Acá es donde deberá ingresar el estado del paciente y todo lo que se relacione a ello."
              ></v-textarea>

              <v-select
                v-model="historia.medicamento"
                :items="medicamento"
                :rules="medicamentoRules"
                label="Medicamento"
                required
              ></v-select>

              <v-text-field
                v-model="historia.posologia"
                :rules="posologiaRules"
                label="Receta"
                required
              ></v-text-field>

              <v-text-field
                v-model="historia.remision"
                :rules="remisionRules"
                label="Remision"
                required
              ></v-text-field>

              <v-btn
                :disabled="!valid"
                color="success"
                class="mr-4"
                @click="saveHistoria()"
              >
                Agregar
              </v-btn>

              <v-btn color="error" class="mr-4" @click="reset">
                Reiniciar
              </v-btn>
            </v-form>
          </v-card-text>
        </center>
      </v-card>
    </template>
  </div>
</template>

<script>
const url_apiesp = "http://localhost:3001/especialidades/";
const url_apimed = "http://localhost:3001/medicamentos/";
const url_apihistoria = "http://localhost:3001/Historias_clinicas/";
const url_apiexamen = "http://localhost:3001/examenes/";
export default {
  layout: "medico",
  beforeMount() {
    this.getEspecialidad();
    this.getMedicamento();
    this.loadUser();
  },
  data: () => ({
    dialog: false,
    valid: true,
    auxiliar: {},
    paciente: {},
    especialidad: [],
    medicamento: [],
    CamposExamen: {
      idpaciente: "",
      idauxiliar: "",
      arealab: "",
      nombre: "",
      fecha: ""
    },
    rules: {
      required: [v => !!v || "El campo es obligatorio"]
    },
    historia: {
      fecha: new Date().toISOString().substr(0, 10),
      especialidad: "",
      cedula_medico: "",
      cedula_paciente: "",
      peso: "",
      estatura: "",
      motivo_consulta: "",
      descripcion: "",
      medicamento: "",
      posologia: "",
      remision: ""
    },
    menu: "",
    area: [
      "Hematología y coagulación",
      "Químicas",
      "Orina y heces",
      "Inmunología",
      "Serología",
      "	Microbiología",
      "Citometría de flujo y biología molecular"
    ],
    idRules: [v => !!v || "Id must be valid"],
    cedula_medico: "",
    cedula_medicoRUles: [v => !!v || "La cedula del medico must be valid"],
    cedula_paciente: "",
    cedula_pacienteRUles: [v => !!v || "La cedula del paciente must be valid"],
    posologiaRules: [v => !!v || "Posologia must be valid"],
    especialidadRules: [v => !!v || "Especialidad is required"],
    pesoRules: [v => !!v || "Peso is required"],
    estaturaRules: [v => !!v || "Estatura is required"],
    medicamentoRules: [v => !!v || "Medicamento is required"],
    remisionRules: [v => !!v || "Remision is required"],
    select: null
  }),

  methods: {
    loadUser() {
      let stringUser = localStorage.getItem("user-doctor");
      this.auxiliar = JSON.parse(stringUser);
      let stringUser2 = localStorage.getItem("estres");
      this.paciente = JSON.parse(stringUser2);
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
    async getMedicamento() {
      try {
        let response = await this.$axios.get(url_apimed);
        this.med = response.data;
        for (var i of this.med) {
          this.medicamento.push(i.nombre);
        }
      } catch (error) {
        console.error(error);
      }
    },

    async GuardarExamen() {
      if (this.$refs.formHistoria.validate()) {
        this.CamposExamen.idpaciente = this.paciente.id;
        this.historia.cedula_paciente = this.paciente.id;
        this.CamposExamen.idauxiliar = this.auxiliar.id;
        this.historia.cedula_medico = this.auxiliar.id;
        this.CamposExamen.fecha =
          new Date().getDay() +
          "/" +
          new Date().getMonth() +
          "/" +
          new Date().getFullYear();
        // Crear un nuevo objeto con la info del usuario
        try {
          let examen = Object.assign({}, this.CamposExamen);
          let response = await this.$axios.post(url_apiexamen, examen);
          this.$swal.fire({
            type: "success",
            title: "Operación exitosa.",
            text: "El examen se guardó correctamente."
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
          text: "Hay campos que deben ser diligenciados."
        });
      }
    },
    async saveHistoria() {
      try {
        if (this.$refs.formHistoria.validate()) {
          let stringUser = JSON.parse(localStorage.getItem("user-doctor"));
          let stringUser2 = JSON.parse(localStorage.getItem("estres"));
          console.log(stringUser.id);
          console.log(stringUser2.id);
          this.historia.cedula_medico = stringUser.id;
          this.historia.cedula_paciente = stringUser2.id;
          // Crear un nuevo objeto con la info del usuario
          try {
            let historia = Object.assign({}, this.historia);
            let response = await this.$axios.post(url_apihistoria, historia);
            this.$swal.fire({
              type: "success",
              title: "Operación exitosa.",
              text: "La entrada se guardó correctamente."
            });
            this.$router.push("Historia_clinica");
          } catch (error) {
            console.log(error);
          }
        } else {
          this.$swal.fire({
            type: "warning",
            title: "Formulario incompleto.",
            text: "Hay campos que deben ser diligenciados."
          });
        }
      } catch (error) {
        console.log(error);
      }
    },
    reset() {
      this.$refs.formHistoria.reset();
    }
  }
};
</script>
