<template>
  <v-app>
    <!-- Menu hamburguesa -->
    <v-navigation-drawer v-model="openMenu" color="#8bcdcd" fixed app>
      <template v-slot:prepend> </template>

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
          <v-btn block @click="cerrarSesion()">
            Cerrar sesion
            <v-icon> mdi-exit-to-app </v-icon>
          </v-btn>
        </div>
      </template>
    </v-navigation-drawer>

    <!--Barra de navegacion superior-->
    <v-app-bar :clipped-left="clipped" fixed app color="#8bcdcd">
      <!--Icono que controla el menú hamburguesa -->
      <v-app-bar-nav-icon @click.stop="openMenu = !openMenu" />

      <v-toolbar-items>
        <img
          src="../static/images/logoapolo.PNG"
          max-width="64"
          min-width="64"
        />
      </v-toolbar-items>
    </v-app-bar>
    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>
    <v-footer :absolute="!fixed" app>
      <v-spacer></v-spacer>
      <span>Codex &copy; {{ new Date().getFullYear() }}</span>
      <small>Administrador</small>
      <v-spacer></v-spacer>
    </v-footer>
    <!--Barra de navegacion superior-->
    <v-app-bar :clipped-left="clipped" fixed app color="#8bcdcd">
      <!--Icono que controla el menú hamburguesa -->
      <v-app-bar-nav-icon @click.stop="openMenu = !openMenu" />

      <v-toolbar-items>
        <img
          src="../static/images/logoapolo.PNG"
          max-width="64"
          min-width="64"
        />
      </v-toolbar-items>
    </v-app-bar>
  </v-app>
</template>

<script>
export default {
  beforeMount() {
    this.loadUser();
  },
  data() {
    return {
      openMenu: false,
admin:null,
      items: [
        {
          id: "adminCrearUsuarios",
          icon: "mdi-account",
          title: "Usuarios",
          to: "/Administrador/adminUsuarios",
        },
        {
          id: "adminHistoriasClinicas",
          icon: "mdi-clipboard-edit-outline",
          title: "Historias Clinicas",
          to: "/Administrador/adminHistoriaClinica",
        },
        {
          id: "adminMedicamentos",
          icon: "mdi-pill",
          title: "Medicamentos",
          to: "/Administrador/adminMedicamentos",
        },
        {
          id: "adminEspecialidades",
          icon: "mdi-star-box-outline",
          title: "Especialidades",
          to: "/Administrador/adminEspecialidades",
        },
      ],
      title: "Vuetify.js",
    };
  },
  methods: {
    loadUser() {
      let stringUser=localStorage.getItem("admin-system");
      this.admin = JSON.parse(stringUser);
    },
    cerrarSesion(){
localStorage.clear();
this.$router.push("/");
    },
    }
};
</script>

<style scoped>
#app {
  background: url("../static/images/fondoAzul.jpeg") no-repeat center fixed !important;
  background-size: 1366px 788px;
}
</style>