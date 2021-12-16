<template>
  <v-app>
    <!-- Menu hamburguesa -->
    <v-navigation-drawer v-model="openMenu" color="#a4ebf3" fixed app>
      <template v-slot:prepend>
        <v-list-item two-line>
          <v-avatar size="180">
            <v-img
              src=
              "https://images.generated.photos/hCJpcCDshv_zQx6eNs4v4S_AzD4Uf2JKheA1-Iap0d8/rs:fit:512:512/wm:0.95:sowe:18:18:0.33/Z3M6Ly9nZW5lcmF0/ZWQtcGhvdG9zL3Yz/XzA1MDU3MjIuanBn.jpg"
            ></v-img>
          </v-avatar>
        </v-list-item>

        <v-list-item-content>
          <v-list-item-title class="text-md-center">
            {{ usuario.nombre }}{{ usuario.apellidos }}
          </v-list-item-title>
          <v-list-item-subtitle class="text-md-center">{{
            usuario.especialidad
          }}</v-list-item-subtitle>
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
          <v-btn @click="cerrarSesion()" block text to="/">
            Logout
            <v-icon> mdi-exit-to-app </v-icon>
          </v-btn>
        </div>
      </template>
    </v-navigation-drawer>

    <!--Barra de navegacion superior-->
    <v-app-bar :clipped-left="clipped" fixed app color="#ccf2f4">
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
      <small>Historias clinicas</small>
      <v-spacer></v-spacer>
    </v-footer>
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
      items: [
        {
          id: "homemeedico",
          icon: "mdi-home",
          title: "Home",
          to: "/Medico/MedicoHome"
        },
        {
          id: "perfilmeedico",
          icon: "mdi-account",
          title: "Perfil",
          to: "/Medico/MedicoPerfil"
        }
      ],
      title: "Vuetify.js"
    };
  },
  methods: {
    loadUser() {
      let stringUser = localStorage.getItem("user-doctor");
      this.usuario = JSON.parse(stringUser);
      console.log(stringUser);
    },
    cerrarSesion() {
      localStorage.clear();
      this.$router.push("/");
    }
  }
};
</script>
<style scoped>
#app {
  background: url("../static/images/fondoAzul.jpeg")  !important;
  background-size: cover;
}
</style>