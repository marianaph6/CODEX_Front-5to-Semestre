<template>
  <v-container fluid>
    <v-data-iterator
      :items="items"
      :items-per-page.sync="itemsPerPage"
      :page.sync="page"
      hide-default-footer
    >
      <template v-slot:header>
        <v-toolbar class="mb-2" color="#3797a4" dark flat>
          <v-row>
            <v-col cols="12" sm="5">
              <v-select
                prepend-inner-icon="mdi-magnify"
                solo-inverted
                hide-details
                :items="especializaciones"
                label="Buscar por especialización"
                @change="filterEspecializacion"
                v-model="especializacion_seleccionada"
              ></v-select>
            </v-col>
            <v-col cols="12" sm="5">
              <v-menu
                ref="menu"
                v-model="menu"
                :close-on-content-click="false"
                :return-value.sync="date"
                transition="scale-transition"
                offset-y
                max-width="290px"
                min-width="auto"
              >
                <template v-slot:activator="{ on, attrs }">
                  <v-text-field
                    v-model="date"
                    solo-inverted
                    hide-details
                    label="Buscar por fecha"
                    prepend-inner-icon="mdi-calendar"
                    readonly
                    v-bind="attrs"
                    v-on="on"
                  ></v-text-field>
                </template>
                <v-date-picker
                  @change="filterMes"
                  v-model="date"
                  type="month"
                  no-title
                  scrollable
                >
                  <v-spacer></v-spacer>
                  <v-btn text color="primary" @click="menu = false">
                    Cancel
                  </v-btn>
                  <v-btn text color="primary" @click="$refs.menu.save(date)">
                    OK
                  </v-btn>
                </v-date-picker>
              </v-menu>
            </v-col>
            <v-col cols="12" sm="2" justify="center" align="center">  
              <v-btn
                class="text-center"
                small
                color="#ee6f57"
                dark
                @click="limpiarFiltros"
              >
                Limpiar filtros
              </v-btn>
               <v-icon big @click="HistoriasPaciente()"> mdi-plus-box-multiple </v-icon>
            </v-col>
          </v-row>
        </v-toolbar>
      </template>

      <template v-slot:default="props">
        <v-row>
          <v-col
            v-for="item in props.items"
            :key="item.especializacion"
            cols="12"
          >
            <v-card>
              <v-divider></v-divider>

              <v-list dense>
                <v-list-item>
                  <v-list-item-content>Número de historia:</v-list-item-content>
                  <v-list-item-content class="align-end">
                    {{ item.id }}
                  </v-list-item-content>
                </v-list-item>

                <v-list-item>
                  <v-list-item-content>Fecha:</v-list-item-content>
                  <v-list-item-content class="align-end">
                    {{ item.fecha }}
                  </v-list-item-content>
                </v-list-item>

                <v-list-item>
                  <v-list-item-content>Motivo de consulta:</v-list-item-content>
                  <v-list-item-content class="align-end">
                    {{ item.motivo_consulta }}
                  </v-list-item-content>
                </v-list-item>

                <v-list-item>
                  <v-list-item-content>Descripción:</v-list-item-content>
                  <v-list-item-content class="align-end">
                    {{ item.descripcion }}
                  </v-list-item-content>
                </v-list-item>

                <v-list-item>
                  <v-list-item-content>Peso:</v-list-item-content>
                  <v-list-item-content class="align-end">
                    {{ item.peso }}
                  </v-list-item-content>
                </v-list-item>

                <v-list-item>
                  <v-list-item-content>Estatura:</v-list-item-content>
                  <v-list-item-content class="align-end">
                    {{ item.estatura }}
                  </v-list-item-content>
                </v-list-item>

                <v-list-item>
                  <v-list-item-content>Médico:</v-list-item-content>
                  <v-list-item-content class="align-end">
                    {{ item.cedula_medico }}
                  </v-list-item-content>
                </v-list-item>

                <v-list-item>
                  <v-list-item-content>Medicamentos:</v-list-item-content>
                  <v-list-item-content class="align-end">
                    {{ item.medicamento }}: {{ item.posologia }}
                  </v-list-item-content>
                </v-list-item>

                <v-list-item>
                  <v-list-item-content>Remisiones:</v-list-item-content>
                  <v-list-item-content class="align-end">
                    {{ item.remision }}
                  </v-list-item-content>
                </v-list-item>
              </v-list>
            </v-card>
          </v-col>
        </v-row>
      </template>

      <template v-slot:footer>
        <v-row class="mt-2" align="center" justify="center">
          <v-spacer></v-spacer>
          <span class="mr-4 grey--text">
            Page {{ page }} of {{ numberOfPages }}
          </span>
          <v-btn fab dark color="#3797a4" class="mr-1" @click="formerPage">
            <v-icon>mdi-chevron-left</v-icon>
          </v-btn>
          <v-btn fab dark color="#3797a4" class="ml-1" @click="nextPage">
            <v-icon>mdi-chevron-right</v-icon>
          </v-btn>
        </v-row>
      </template>
    </v-data-iterator>
  </v-container>
</template>

<script>
const url_apiesp = "http://localhost:3001/especialidades/";
export default {
  layout: "medico",
  beforeMount() {
    console.log(localStorage.getItem("estres"));
    //this.loadUser();
    this.getHistorias();
    this.getEspecialidad();

  },
  data: () => ({
    date: new Date().toISOString().substr(0, 7),
    menu: false,
    paciente: {},
    page: 1,
    itemsPerPage: 3,
    especializacion_seleccionada: null,
    filter: {},
    especializaciones: [],
    esp: [],
    headers: [],
    items: [],
    historias: [],
    datos: []
  }),
  computed: {
    numberOfPages() {
      return Math.ceil(this.items.length / this.itemsPerPage);
    }
  },

  methods: {
    nextPage() {
      if (this.page + 1 <= this.numberOfPages) this.page += 1;
    },
    formerPage() {
      if (this.page - 1 >= 1) this.page -= 1;
    },
    updateItemsPerPage(number) {
      this.itemsPerPage = number;
    },

    filterEspecializacion() {
      console.log(this.especializacion_seleccionada);
      this.items = this.historias.filter(
        item => item.especialidad === this.especializacion_seleccionada
      );
    },
    filterMes() {
      this.items = this.historias.filter(
        item => item.fecha.substr(0, 7) == this.date
      );
    },
    async getEspecialidad() {
      try {
        let response = await this.$axios.get(url_apiesp);
        this.esp = response.data;
        for (var i of this.esp) {
          this.especializaciones.push(i.nombre);
        }
      } catch (error) {
        console.error(error);
      }
    },


    limpiarFiltros() {
      this.items = this.historias;
    },

    loadUser() {
      this.paciente = JSON.parse(stringUser);
    },

    async getHistorias() {
      try {
        let response = await this.$axios.get(
          "http://localhost:3001/Historias_clinicas"
        );
        let stringUser = JSON.parse(localStorage.getItem("estres"));
        console.log(stringUser.id);
        this.datos = response.data;
        for (var i of this.datos) {
          if (stringUser.id == i.cedula_paciente) {
            this.items.push(i);
            this.historias.push(i);
          }
        }
      } catch (error) {
        console.error(error);
      }
    },
    HistoriasPaciente() {
      localStorage.getItem("estres");
      this.$router.push("Agregar_Historia");
    }
  }
};
</script>
