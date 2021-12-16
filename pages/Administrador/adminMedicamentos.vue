<template>
  <v-template>
    <center>
      <v-card style="width: 800px" elevation="12">
        <v-toolbar color="#3797a4">
          <v-card-title class="#0c354a--text">
            <center>Agregar Medicamentos</center>
          </v-card-title>
          <v-dialog v-model="dialog" max-width="800px">
            <v-card>
              <v-toolbar color="#3797a4">
                <v-card-title>
                  Editar Medicamento
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
                    ref="formEditarMedicamento"
                    v-model="valid"
                    lazy-validation
                  >
                    <v-row>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="DetalleMedicamento.id"
                          label="Id Medicamento"
                          readonly
                        >
                        </v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="DetalleMedicamento.nombre"
                          label="Nombre medicamento"
                          :rules="rules.required"
                        ></v-text-field>
                      </v-col>

                      <v-col cols="12" sm="8" md="4">
                        <v-text-field
                          v-model="DetalleMedicamento.descripcion"
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
                <v-btn color="blue darken-1" text @click="editarMedicamento()">
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
          <v-form ref="formMedicamento" v-model="valid" lazy-validation>
            <v-text-field
              v-model="CamposMedicamento.nombre"
              :rules="rules.required"
              label="Nombre Medicamento"
              style="height: 100px"
              required
            ></v-text-field>
            <v-text-field
              v-model="CamposMedicamento.descripcion"
              label="Descripcion"
              style="height: 100px"
              :rules="rules.required"
            ></v-text-field>
            <center>
              <v-btn
                class="white--text"
                color="#ee6f57"
                @click="agregarMedicamento()"
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
          Base de datos de Medicamentos
          <v-spacer></v-spacer>
          <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            label="Buscar"
            single-line
            hide-details
          ></v-text-field>
        </v-card-title>
        <v-data-table :headers="headers" :items="medicamento" :search="search">
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
const url_apimedicamento = "http://localhost:3001/medicamentos/";
export default {
  beforeMount() {
    //this.loadUser();
    this.getMedicamento();
  },
  layout: "admin",
  data: () => ({
    dialog: false,
    rules: {
      required: [(v) => !!v || "El campo es obligatorio"],
    },
    search: "",
    medicamento: [],

    CamposMedicamento: {
      nombre: "",
      descripcion: "",
    },

    DetalleMedicamento: {
      nombre: "",
      descripcion: "",
      id: 0,
    },
    med:{},

    headers: [
      {
        text: "Id",
        align: "start",
        sortable: false,
        value: "id",
      },
      { text: "Nombre del Medicamento", value: "nombre" },
      { text: "Descripcion", value: "descripcion" },

      { text: "Operaciones", value: "actions" },
    ],
  }),
  methods: {
    validate() {
      this.$refs.form.validate();
    },
    resetForm() {
      this.CamposMedicamento.nombre = "";
      this.CamposMedicamento.descripcion = "";
    },
    async getMedicamento() {
      try {
        let response = await this.$axios.get(url_apimedicamento);
        this.medicamento = response.data;
      } catch (error) {
        console.error(error);
      }
    },
    Editar(item) {
      //console.log(item);
      this.DetalleMedicamento = Object.assign({}, item);
      localStorage.setItem("medicamento-editar", JSON.stringify(item));
      this.dialog = true;
    },

    async editarMedicamento() {
      
      if (this.$refs.formEditarMedicamento.validate()) {
        let medicamento = Object.assign({}, this.DetalleMedicamento);
        let response = await this.$axios.put(
          url_apimedicamento + this.DetalleMedicamento.id,
          medicamento
        );
        this.$swal.fire({
          type: "success",
          title: "Operación exitosa.",
          text: "La información del medicamento se actualizo correctamente.",
        });
        
        this.dialog = false;
        this.getMedicamento();
        localStorage.setItem("medicamento-editar", "");
        

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

    async agregarMedicamento() {
      if (this.$refs.formMedicamento.validate()) {
        // Crear un nuevo objeto con la info del usuario
        try {
          let medicamento = Object.assign({}, this.CamposMedicamento);
          let response = await this.$axios.post(
            url_apimedicamento,
            medicamento
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

        this.getMedicamento();
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
          title: "¿Está seguro de eliminar el medicamento?",
          text: "Al borrar el medicamento este se prodrá recuperar.",
          allowEscapeKey: false,
          allowOutsideClick: false,
          showCancelButton: true,
        })
        .then(async (result) => {
          if (result.value) {
            try {
              let url = url_apimedicamento + item.id;
              await this.$axios.delete(url);
              this.$swal.fire({
                type: "success",
                title: "Operación exitosa.",
                text: "El medicamento se eliminó correctamente.",
              });
              this.getMedicamento();
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