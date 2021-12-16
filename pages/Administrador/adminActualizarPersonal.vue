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
                    disabled
                  ></v-text-field>
                </v-col>
                <v-col cols="12" sm="6">
                  <v-text-field
                    v-model="personal.apellidos"
                    label="Apellidos"
                    :rules="rules.required"
                    required
                    disabled
                  ></v-text-field>
                </v-col>
              </v-row>
              <v-text-field
                v-model="personal.id"
                label="Cédula"
                :rules="rules.required"
                required
                disabled
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
                        v-bind="attrs"
                        v-on="on"
                        disabled
                        
                      ></v-text-field>
                    </template>
                    <v-date-picker
                    
                      v-model="personal.fecha_nac"
                      @input="menu1 = false"
                      disabled
                    ></v-date-picker>
                  </v-menu>
                </v-col>
                <v-col cols="12" sm="4">
                  <v-text-field
                    v-model="personal.edad"
                    label="Edad"
                    :rules="rules.required"
                    required
                    disabled
                  ></v-text-field>
                </v-col>
                <v-col cols="12" sm="4">
                  <v-select
                    v-model="personal.sexo"
                    :items="sexo"
                    label="Sexo"
                    :rules="rules.required"
                    required
                    disabled
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
                    readonly
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
              <v-btn
                class="white--text"
                color="#ee6f57"
                @click="updatePersonal()"
              >
                Actualizar personal medico
              </v-btn>
            </v-form>
          </v-card-text>
        </v-card>
</template>

<script>
const url_apimedico = "http://localhost:3001/medicos/";
const url_apiauxiliar = "http://localhost:3001/auxiliares/";
const url_apiesp = "http://localhost:3001/especialidades/";
export default {
   beforeMount() {
    
    this.loadUser();
    this.getEspecialidad();
  },
    layout: "admin",
    data: () => ({
    date: new Date().toISOString().substr(0, 10),
    menu1: false,
    valid: true,
    sexo: ["Masculino", "Femenino"],
    estado_civil: ["Soltero", "Casado", "Divorciado", "Viudo"],
    departamentos: ["Antioquia", "Arauca", "Atlántico", "Bolívar"],
    ciudades: ["Medellín", "Bogotá", "Barranquilla", "Cartagena"],
    especialidad: [],
    esp:[],
    perfil: ["Medico", "Auxiliar"],

    personal: {
      
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

    loadUser() {
      //localStorage.setItem("med-editar", JSON.stringify(item));
      let stringUser=localStorage.getItem("med-editar");
      if(stringUser==""){
        let stringUser=localStorage.getItem("aux-editar");
        this.personal = JSON.parse(stringUser)
      }else{
      this.personal = JSON.parse(stringUser);
      }
    },

    async getEspecialidad() {
      try {
        let response = await this.$axios.get(url_apiesp);
        this.esp= response.data;
        for(var i of this.esp){
            this.especialidad.push(i.nombre);
        }

      } catch (error) {
        console.error(error);
      }
    },

    async updatePersonal() {
      if (this.$refs.formPersonalMedico.validate()) {
        if(this.personal.perfil=="Medico"){
          let usuario = Object.assign({}, this.personal);
        let response = await this.$axios.put(url_apimedico + this.personal.id, usuario);
        this.$swal.fire({
          type: "success",
          title: "Operación exitosa.",
          text: "El perfil del medico se actualizo correctamente.",
        });
        localStorage.setItem("med-editar", "");
        this.$router.push("adminUsuarios");

        }
        else if(this.personal.perfil=="Auxiliar"){
          let usuario = Object.assign({}, this.personal);
        let response = await this.$axios.put(url_apiauxiliar + this.personal.id, usuario);
        this.$swal.fire({
          type: "success",
          title: "Operación exitosa.",
          text: "El perfil del auxiliar se actualizo correctamente.",
        });
        localStorage.setItem("aux-editar", "");
        this.$router.push("adminUsuarios");
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
    /**
     * Enviar una solicitud (Request) en un método update
     * Para modificar el usuario
     */
    

    

    
  },
  

}
</script>

<style>

</style>