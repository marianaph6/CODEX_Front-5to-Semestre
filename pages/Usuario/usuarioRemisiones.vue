<template>
  <v-card>
    <v-card-title>
      Remisiones
      <v-spacer></v-spacer>
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="Buscar"
        single-line
        hide-details
      ></v-text-field>
    </v-card-title>
    <v-data-table
      :headers="headers"
      :items="remisiones"
      :search="search"
    ></v-data-table>
  </v-card>
</template>


<script>
export default {
  layout: "usuario",
  beforeMount() {
    this.loadUser();
    this.getRemisiones();
  },
  data() {
    return {
      search: "",
      headers: [
        {
          text: "Tipo de remisión",
          align: "start",
          sortable: false,
          value: "remision",
        },
        { text: "Número de orden", value: "id" },
        { text: "Fecha de remisión", value: "fecha" },
      ],
      datos: [],
      remisiones: [],
    };
  },
  methods: {
    loadUser() {
      let stringUser = localStorage.getItem("user-paciente");
      this.usuario = JSON.parse(stringUser);
    },

    async getRemisiones(){
      try {
        let response = await this.$axios.get(
          "http://localhost:3001/Historias_clinicas"
        );
        this.datos = response.data;
        for (var i of this.datos) {
          if (this.usuario.id == i.cedula_paciente) {
            this.remisiones.push(i);
          }
        }
      } catch (error) {
        console.error(error);
      }
    }
  },
};
</script>