<template>
  <v-card class="overflow-hidden">
    <v-toolbar flat>
      <v-toolbar-title class="#0c354a">Datos personales</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn
        class="white--text"
        color="#ee6f57"
        fab
        small
        @click="isEditing = !isEditing"
      >
        <v-icon v-if="isEditing"> mdi-close </v-icon>
        <v-icon v-else> mdi-pencil </v-icon>
      </v-btn>
    </v-toolbar>
    <v-card-text>
      <v-form ref="formUsuario" v-model="valid" lazy-validation>
        <v-row>
          <v-col cols="12" sm="6">
            <v-text-field v-model="usuario.nombre" label="Nombres" disabled  ></v-text-field>
          </v-col>
          <v-col cols="12" sm="6">
            <v-text-field v-model="usuario.apellidos" label="Apellidos" disabled></v-text-field>
          </v-col>
        </v-row>
        <v-text-field v-model="usuario.id" label="Cédula" disabled></v-text-field>
        <v-row>
          <v-col cols="12" sm="4">
            <v-text-field v-model="usuario.fecha_nac" label="Fecha de Nacimiento" disabled></v-text-field>
          </v-col>
          <v-col cols="12" sm="4">
            <v-text-field v-model="usuario.edad" label="Edad" disabled></v-text-field>
          </v-col>
          <v-col cols="12" sm="4">
            <v-text-field v-model="usuario.sexo" label="Sexo" disabled></v-text-field>
          </v-col>
        </v-row>
        <!--DATOS QUE SE PUEDEN ACTUALIZAR -->
        <v-text-field
          v-model="usuario.ocupacion"
          label="Ocupación"
          :rules="rules.required"
          :disabled="!isEditing"
        ></v-text-field>
        <v-select
          v-model="usuario.estado_civil"
          :items="estado_civil"
          label="Estado civil"
          :rules="rules.required"
          :disabled="!isEditing"
        ></v-select>
        <v-text-field
          v-model="usuario.correo"
          label="Correo"
          :rules="rules.required"
          :disabled="!isEditing"
        ></v-text-field>
        <v-text-field
          v-model="usuario.telefono"
          label="Teléfono"
          :rules="rules.required"
          :disabled="!isEditing"
        ></v-text-field>
        <v-row>
          <v-col cols="12" sm="6">
            <v-select
              v-model="usuario.departamento"
              :items="departamentos"
              label="Departamento"
              :rules="rules.required"
              :disabled="!isEditing"
            ></v-select>
          </v-col>
          <v-col cols="12" sm="6">
            <v-select
              v-model="usuario.ciudad"
              :items="ciudades"
              label="Ciudad"
              :rules="rules.required"
              :disabled="!isEditing"
            ></v-select>
          </v-col>
        </v-row>
        <v-text-field
          v-model="usuario.direccion"
          :disabled="!isEditing"
          label="Dirección"
          :rules="rules.required"
        ></v-text-field>
      </v-form>
    </v-card-text>
    <v-divider></v-divider>
    <v-card-actions>
      <v-spacer></v-spacer>
      <v-btn
        class="white--text"
        :disabled="!isEditing"
        color="#ee6f57"
        @click="updateUsuario"
      >
        Guardar
      </v-btn>
    </v-card-actions>
    <v-snackbar v-model="hasSaved" :timeout="2000" absolute bottom left>
      Datos actualizados!
    </v-snackbar>
  </v-card>
</template>


<script>
const url_api = "http://localhost:3001/pacientes/";
export default {
  layout: "usuario",
  data: () => ({
    valid: true,
    hasSaved: false,
    isEditing: null,
    model: null,
    usuario: {},
    estado_civil: ["Soltero", "Casado", "Divorciado", "Viudo"],
    departamentos: ["Antioquia", "Arauca", "Atrlántico", "Bolívar"],
    ciudades: [],
    arrayCiudades:[],
    rules: {
      required: [(v) => !!v || "El campo es obligatorio"],
    },
  }),

  beforeMount() {
    this.getUsuario();
    this.getCiudades();
  },

  methods: {
    getUsuario() {
      let stringUser = localStorage.getItem("user-paciente");
      this.usuario = JSON.parse(stringUser);
    },

     async updateUsuario() {
      if (this.$refs.formUsuario.validate()) {
        // Crear un nuevo objeto con la info del usuario
        let usuario = Object.assign({}, this.usuario);
        let response = await this.$axios.put(url_api + this.usuario.id, usuario);
        this.$swal.fire({
          type: "success",
          title: "Operación exitosa.",
          text: "El perfil se actualizo correctamente.",
        });
        this.isEditing = !this.isEditing;
      } else {
        this.$swal.fire({
          type: "warning",
          title: "Formulario incompleto.",
          text: "Hay campos que deben ser diligenciados.",
        });
      }
    },

    //No funciona :(
    async getCiudades() {
      try {
        let response = await this.$axios.get("http://localhost:3001/ciudades");
        this.arrayCiudades = response.data;
        for(var i of this.arrayCiudades){
          this.ciudades.push(i.ciudad);
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