<template>
  <v-card>
    <v-card-title>
      Medicamentos del paciente
      <v-spacer></v-spacer>
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
      ></v-text-field>
    </v-card-title>
    <v-data-table
      :headers="headers"
      :items="desserts"
      :search="search"
    ></v-data-table>
  </v-card>
</template>


<script>
  export default {
    layout: 'usuario',
    data () {
      return {
        search: '',
        headers: [
          {
            text: 'Nombre del medicamento',
            align: 'start',
            sortable: false,
            value: 'nombre',
          },
          { text: 'Posología', value: 'posologia' },
          { text: 'Cantidad', value: 'cantidad' },
         
        ],
        desserts: [
          {
            nombre: ' Augmentine "125" suspensión, (125/31,25 mg/5 ml)',
            posologia: 'cada 8 horas',
            cantidad: '4,28 ml',
          },
          {
            nombre: 'Ácido acetilsalicílico (200/75,5 mg/1 ml)',
            posologia: 'cada 4 horas',
            cantidad: '1,07 ml ',
          },
          {
            nombre: ' Paracetamol, (180/31,25 mg/5 ml)',
            posologia: 'cada 12 horas',
            cantidad: '2,26 ml',
          },
         
        ],
      }
    },
  }
</script>

<style>

</style>