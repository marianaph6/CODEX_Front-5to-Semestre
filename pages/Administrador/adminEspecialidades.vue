<template>
  <v-template>
    <center>
      <v-card style="width: 800px" elevation="12">
        <v-toolbar color="#3797a4">
          <v-card-title class="#0c354a--text">
            <center>Agregar Especialidades</center>
          </v-card-title>
          <v-dialog v-model="dialog" max-width="800px">
            <v-card>
              <v-toolbar color="#3797a4">
                <v-card-title>
                  Editar Especialidad
                  <v-spacer>
                    <v-avatar color="#ee6f57">
                      <v-icon dark size="40"> mdi-account </v-icon>
                    </v-avatar>
                  </v-spacer>
                </v-card-title>
              </v-toolbar>

              <v-card-text>
                <v-container>
                  <v-form
                    ref="formEditarEspecialidad"
                    v-model="valid"
                    lazy-validation
                  >
                    <v-row>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="DetalleEspecialidad.id"
                          label="Id Especialidad"
                          readonly
                        >
                        </v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="DetalleEspecialidad.nombre"
                          label="Nombre Especialidad"
                          :rules="rules.required"
                        ></v-text-field>
                      </v-col>

                      <v-col cols="12" sm="8" md="4">
                        <v-text-field
                          v-model="DetalleEspecialidad.descripcion"
                          label="Descripción"
                          :rules="rules.required"
                          
                        ></v-text-field>
                      </v-col>
                    </v-row>
                  </v-form>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="editarEspecialidad()">
                  Editar
                </v-btn>
                <v-btn color="blue darken-1" text @click="close">
                  Cerrar
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
        <v-card-text>
          <v-form ref="formEspecialidad" v-model="valid" lazy-validation>
            <v-text-field
              v-model="CamposEspecialidad.nombre"
              :rules="rules.required"
              label="Nombre Especialidad"
              style="height: 100px"
              required
            ></v-text-field>
            <v-text-field
              v-model="CamposEspecialidad.descripcion"
              label="Descripcion"
              style="height: 100px"
              :rules="rules.required"
            ></v-text-field>
            <center>
              <v-btn
                class="white--text"
                color="#ee6f57"
                @click="agregarEspecialidad()"
              >
                Agregar
              </v-btn>
            </center>
          </v-form>
        </v-card-text>
      </v-card>
    </center>
    <v-container>
      <v-card>
        <v-card-title>
          Base de datos de Especialidades
          <v-spacer></v-spacer>
          <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            label="Buscar"
            single-line
            hide-details
          ></v-text-field>
        </v-card-title>
        <v-data-table :headers="headers" :items="especialidad" :search="search">
          <template v-slot:item.actions="{ item }">
            <v-icon small class="mr-2" @click="Editar(item)">
              mdi-pencil
            </v-icon>
            <v-icon small @click="deleteItem(item)">
              mdi-delete
            </v-icon>
          </template>
        </v-data-table>
      </v-card>
    </v-container>
  </v-template>
</template>

<script>
const url_apiespecialidad = "http://localhost:3001/especialidades/";
export default {
  beforeMount() {
    //this.loadUser();
    this.getEspecialidad();
  },
  layout: "admin",
  data: () => ({
    dialog: false,
    rules: {
      required: [(v) => !!v || "El campo es obligatorio"],
    },
    search: "",
    especialidad: [],

    CamposEspecialidad: {
      nombre: "",
      descripcion: "",
    },

    DetalleEspecialidad: {
      nombre: "",
      descripcion: "",
      id: 0,
    },
    esp:{},

    headers: [
      {
        text: "Id",
        align: "start",
        sortable: false,
        value: "id",
      },
      { text: "Nombre de la Especialidad", value: "nombre" },
      { text: "Descripcion", value: "descripcion" },

      { text: "Operaciones", value: "actions" },
    ],
  }),
  methods: {
    validate() {
      this.$refs.form.validate();
    },
    resetForm() {
      this.CamposEspecialidad.nombre = "";
      this.CamposEspecialidad.descripcion = "";
    },
    async getEspecialidad() {
      try {
        let response = await this.$axios.get(url_apiespecialidad);
        this.especialidad = response.data;
      } catch (error) {
        console.error(error);
      }
    },
    Editar(item) {
      //console.log(item);
      this.DetalleEspecialidad = Object.assign({}, item);
      localStorage.setItem("especialidad-editar", JSON.stringify(item));
      this.dialog = true;
    },

    async editarEspecialidad() {
      
      if (this.$refs.formEditarEspecialidad.validate()) {
        let especialidad= Object.assign({}, this.DetalleEspecialidad);
        let response = await this.$axios.put(
          url_apiespecialidad + this.DetalleEspecialidad.id,
          especialidad
        );
        this.$swal.fire({
          type: "success",
          title: "Operación exitosa.",
          text: "La información del medicamento se actualizo correctamente.",
        });
        
        this.dialog = false;
        this.getEspecialidad();
        localStorage.setItem("especialidad-editar", "");
        

        // Crear un nuevo objeto con la info del usuario
      } else {
        this.$swal.fire({
          type: "warning",
          title: "Formulario incompleto.",
          text: "Hay campos que deben ser diligenciados.",
        });
      }
    },
    close() {
      this.dialog = false;
    },

    async agregarEspecialidad() {
      if (this.$refs.formEspecialidad.validate()) {
        // Crear un nuevo objeto con la info del usuario
        try {
          let especialidad = Object.assign({}, this.CamposEspecialidad);
          let response = await this.$axios.post(
            url_apiespecialidad,
            especialidad
          );
          this.$swal.fire({
            type: "success",
            title: "Operación exitosa.",
            text: "El medicamento se guardó correctamente.",
          });
        } catch (error) {
          console.log(error);
        }
        this.resetForm();

        this.getEspecialidad();
      } else {
        this.$swal.fire({
          type: "warning",
          title: "Formulario incompleto.",
          text: "Hay campos que deben ser diligenciados.",
        });
      }
    },
     deleteItem(item) {
      this.$swal
        .fire({
          type: "warning",
          title: "¿Está seguro de eliminar la especialidad?",
          text: "Al borrar la especialidad esta se prodrá recuperar.",
          allowEscapeKey: false,
          allowOutsideClick: false,
          showCancelButton: true,
        })
        .then(async (result) => {
          if (result.value) {
            try {
              let url = url_apiespecialidad + item.id;
              await this.$axios.delete(url);
              this.$swal.fire({
                type: "success",
                title: "Operación exitosa.",
                text: "La especialidad se eliminó correctamente.",
              });
              this.getEspecialidad();
            } catch (error) {
              this.$swal.fire({
                type: "error",
                title: "Ha ocurrido un problema al eliminar",
                text: error.toString(),
              });
            }
          }
        });
    },
  },
 
};
</script>

<style>
</style>