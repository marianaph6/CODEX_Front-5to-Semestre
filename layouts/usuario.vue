<template>
  <!-- <v-app v-if="user"> -->
  <v-app>
    <!-- Menu hamburguesa -->
    <v-navigation-drawer v-model="openMenu" color="#8bcdcd" fixed app>
      <template v-slot:prepend>
        <v-list-item two-line class="d-flex justify-center">
          <v-avatar size="180">
            <v-img src="https://randomuser.me/api/portraits/men/85.jpg"></v-img>
          </v-avatar>
        </v-list-item>

        <v-list-item-content class="text-md-center">
          <v-list-item-title class="title"> {{usuario.nombre }} {{ usuario.apellidos}} </v-list-item-title>
          <v-list-item-subtitle>Paciente</v-list-item-subtitle>
        </v-list-item-content>
      </template>

      <v-list>
        <v-list-item
          v-for="item in items"
          :key="item.id"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="item.title" />
          </v-list-item-content>
        </v-list-item>
      </v-list>

      <template v-slot:append>
        <div>
          <v-btn block to="/">
            Cerrar sesión
            <v-icon> mdi-exit-to-app </v-icon>
          </v-btn>
        </div>
      </template>
    </v-navigation-drawer>

    <!--Barra de navegacion superior-->
    <v-app-bar color="#8bcdcd" fixed app>
      <!--Icono que controla el menú hamburguesa -->
      <v-app-bar-nav-icon @click.stop="openMenu = !openMenu" />
    </v-app-bar>
    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>
    <v-footer app>
      <v-spacer></v-spacer>
      <span>Codex &copy; {{ new Date().getFullYear() }}</span>
      <small>Historias clinicas</small>
      <v-spacer></v-spacer>
    </v-footer>
  </v-app>
  <!-- <v-app v-else>
    <v-main>
      <center>
        <h3>Por favor inicie sesión</h3>
        <v-btn color="green darken-1" text to="/">
          Ir al login
        </v-btn>
      </center>
    </v-main>
  </v-app> -->
</template>

<script>
export default {
  beforeMount() {
    this.loadUser();
  },
  data() {
    return {
      usuario: null,
      openMenu: false,
      items: [
        {
          id: "perfil",
          icon: "mdi-account",
          title: "Perfil",
          to: "/Usuario/usuarioPerfil",
        },
        {
          id: "historiaClinia",
          icon: "mdi-clipboard-edit-outline",
          title: "Historial Clínico",
          to: "/Usuario/usuarioHistoria",
        },
        {
          id: "examenes",
          icon: "mdi-note",
          title: "Exámenes",
          to: "/Usuario/usuarioExamenes",
        },
        {
          id: "medicamentos",
          icon: "mdi-pill",
          title: "Medicamentos",
          to: "/Usuario/usuarioMedicamentos",
        },
        {
          id: "remisiones",
          icon: "mdi-text",
          title: "Remisiones",
          to: "/Usuario/usuarioRemisiones",
        },
      ],
      title: "Vuetify.js",
    };
  },
  methods: {
    loadUser() {
      let stringUser = localStorage.getItem("user-paciente");
      this.usuario = JSON.parse(stringUser);
      this.validarRol(this.usuario);
      console.log(stringUser);
    },
    validarRol(usuario) {
      //if(usuario.perfil)
    },
  },
};
</script>
<style scoped>
#app {
  background: url("../static/images/fondoAzul.jpeg") no-repeat center fixed !important;
  background-size: 1366px 788px;
}
</style>