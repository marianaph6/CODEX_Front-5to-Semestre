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
      <v-form ref="formMedico" v-model="valid" lazy-validation>
      <v-row>
        <v-col cols="12" sm="6">
          <v-text-field 
          v-model="usuario.nombre"
          label="Nombres" disabled="isDisabled"></v-text-field>
        </v-col>
        <v-col cols="12" sm="6">
          <v-text-field 
           v-model="usuario.apellidos"
          label="Apellidos" disabled="isDisabled"></v-text-field>
        </v-col>
      </v-row>
      
      <v-text-field  v-model="usuario.id" label="Cédula" disabled="isDisabled"></v-text-field>
      
      <v-row>
        <v-col cols="12" sm="4">
          <v-text-field
           v-model="usuario.fecha_nac"
            label="Fecha de Nacimiento"
            disabled="isDisabled"
          ></v-text-field>
        </v-col>
        <v-col cols="12" sm="4">
          <v-text-field v-model="usuario.edad" label="Edad" disabled="isDisabled"></v-text-field>
        </v-col>
        <v-col cols="12" sm="4">
          <v-text-field v-model="usuario.sexo" label="Sexo" disabled="isDisabled"></v-text-field>
        </v-col>
      </v-row>
      <v-text-field  :rules="rules.required" v-model="usuario.especialidad" label="Especialidad" :disabled="!isEditing"></v-text-field>
      <v-select :rules="rules.required" v-model="usuario.estado_civil" :items="estado_civil" label="Estado civil" :disabled="!isEditing"></v-select>
      <v-text-field :rules="rules.required" v-model="usuario.correo" label="Correo" :disabled="!isEditing"></v-text-field>
      <v-text-field :rules="rules.required" v-model="usuario.telefono" label="Teléfono" :disabled="!isEditing"></v-text-field>
      <v-row>
        <v-col cols="12" sm="6">
          <v-select :rules="rules.required" v-model="usuario.departamento" :items="departamentos" label="Departamento" :disabled="!isEditing"></v-select>
        </v-col>
        <v-col cols="12" sm="6">
          <v-select :rules="rules.required" v-model="usuario.ciudad" :items="ciudades" label="Ciudad" :disabled="!isEditing"></v-select>
        </v-col>
      </v-row>
      <v-text-field :rules="rules.required" v-model="usuario.direccion" :disabled="!isEditing" label="Dirección"></v-text-field>
    
    </v-form>
    </v-card-text>
    <v-divider></v-divider>
    <v-card-actions>
      <v-spacer></v-spacer>
      <v-btn
        class="white--text"
        :disabled="!isEditing"
        color="#ee6f57"
        @click="updateUsuario()"
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
const url_api = "http://localhost:3001/medicos/";
export default {
  beforeMount() {
    this.loadUser();
  },
  layout: "medico",
  data() {
    return {
      rules: {
      required: [(v) => !!v || "El campo es obligatorio"],
    },
      valid: true,
      usuario: {},
      hasSaved: false,
      isEditing: null,
      model: null,
      estado_civil: ["Soltero", "Casado", "Divorciado", "Viudo"],
      departamentos: ["Antioquia", "Arauca", "Atlántico", "Bolívar"],
      ciudades: ["Medellín", "Bogotá", "Barranquilla", "Cartagena"],
    };
  },
  isDisabled: false,

  methods: {
    async updateUsuario() {
      if (this.$refs.formMedico.validate()) {
        // Crear un nuevo objeto con la info del usuario
        let usuario = Object.assign({}, this.usuario);
        let response = await this.$axios.put(url_api + this.usuario.id, usuario);
        this.$swal.fire({
          type: "success",
          title: "Operación exitosa.",
          text: "El item se actualizo correctamente.",
        });
      } else {
        this.$swal.fire({
          type: "warning",
          title: "Formulario incompleto.",
          text: "Hay campos que deben ser diligenciados.",
        });
      }
    },
    loadUser() {
      let stringUser=localStorage.getItem("user-doctor");
      this.usuario = JSON.parse(stringUser);
    },
  },
};
</script>
<style>
</style>