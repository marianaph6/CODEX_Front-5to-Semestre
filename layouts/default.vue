<template>
  <v-app background="../static/images/medico.jpg">
    <br />
    <center>
      <h1 style="font-family: Copperplate">APOLO SALUD</h1>
    </center>
    <br />

    <v-footer :absolute="!fixed" app>
      <v-spacer></v-spacer>
      <span> &copy; {{ new Date().getFullYear() }}</span>
      <small>| Codex</small>
      <v-spacer></v-spacer>
    </v-footer>

    <template>
      <v-row>
        <v-col cols="12" md="7"> </v-col>
        <v-col cols="12" md="4">
          <v-card class="mx-auto" style="width: 350px" elevation="12">
            <v-toolbar color="#3797a4">
              <v-card-title class="#white--text">
                <center>Iniciar sesion</center>
              </v-card-title>
              <v-spacer></v-spacer>
            </v-toolbar>

            <v-form
              ref="formLogin"
              v-model="valid"
              class="pa-3 pt-4"
              lazy-validation
              color="#ccf2f4"
            >
              <v-spacer></v-spacer>

              <v-text-field
                v-model="usuario.user"
                :rules="rules.required"
                label="Usuario"
                style="height: 100px"
                required
              ></v-text-field>

              <v-text-field
                v-model="usuario.password"
                :rules="rules.required"
                label="Contraseña"
                style="height: 100px"
                type="password"
                required
              ></v-text-field>

              <v-select
                solo-inverted
                hide-details
                :items="rol"
                label="Elegir rol"
                v-model="usuario.rol"
              ></v-select>
              <!--@change="filterRol"-->
            </v-form>
            <v-divider></v-divider>
            <v-card-actions>
              <v-btn class="white--text" color="#ee6f57" @click="login()">
                Ingresar
              </v-btn>
              <v-spacer></v-spacer>
              <v-dialog v-model="dialog" max-width="600px">
                <template v-slot:activator="{ on, attrs }">
                  <v-btn
                    color="#0c354a"
                    class="ma-1"
                    v-bind="attrs"
                    v-on="on"
                    plain
                  >
                    ¿Eres administrador?
                  </v-btn>
                </template>

                <v-card>
                  <v-toolbar color="#a4ebf3">
                    <v-card-title class="#0c354a--text">
                      <center>Ingresa tu codigo de administrador</center>
                    </v-card-title>
                    <v-spacer></v-spacer>
                  </v-toolbar>
                  <v-card-text>
                    <v-container>
                      <v-form ref="formAdmin" v-model="valid" lazy-validation>
                        <v-text-field
                          v-model="admin.codigo"
                          :rules="rules.required"
                          label="Codigo"
                          style="height: 100px"
                          required
                        ></v-text-field>
                        <center>
                          <v-btn
                            class="white--text"
                            color="#ff5722"
                            @click="loginadmin()"
                          >
                            Ingresar
                          </v-btn>
                        </center>
                      </v-form>
                    </v-container>
                  </v-card-text>
                </v-card>
              </v-dialog>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>
    </template>
  </v-app>
</template>

<script>
const url_apipaciente = "http://localhost:3001/pacientes/";
const url_apimedico = "http://localhost:3001/medicos/";
const url_apiauxiliar = "http://localhost:3001/auxiliares/";
const url_apiadmin = "http://localhost:3001/administrador/";
export default {
  data: () => ({
    valid: true,
    //usuario: {},
    usuario: {
      user: "",
      password: "",
      rol: ""
    },
    admin: { codigo: "" },
    rules: {
      required: [v => !!v || "El campo es obligatorio"]
    },
    rol: ["Paciente", "Medico", "Auxiliar"]
  }),

  methods: {
    async login() {
      try {
        if (this.$refs.formLogin.validate()) {
          //llamado a la api
          if (this.usuario.rol == "Auxiliar") {
            let response = await this.$axios.get(url_apiauxiliar);
            let users = response.data;
            let findUser = users.find(x => {
              return (
                x.id === this.usuario.user &&
                x.id === this.usuario.password
              );
            });
            if (findUser) {
              delete findUser.password;
              localStorage.setItem("user-auxiliar", JSON.stringify(findUser));
              this.$router.push("Auxiliar/auxiliarHome");
            } else {
              this.$swal.fire({
                type: "error",
                title: "Oops",
                text:
                  "El correo o la contraseña diligenciados son incorrectos.",
                allowEscapeKey: false,
                alowOutsideClick: false
              });
            }
          } else if (this.usuario.rol == "Medico") {
            let response = await this.$axios.get(url_apimedico);
            let users = response.data;
            let findUser = users.find(x => {
              return (
                x.id === this.usuario.user &&
                x.id === this.usuario.password
              );
            });
            if (findUser) {
              localStorage.setItem("user-doctor", JSON.stringify(findUser));
              this.$router.push("Medico/MedicoHome");
            } else {
              this.$swal.fire({
                type: "error",
                title: "Oops",
                text:
                  "El correo o la contraseña diligenciados son incorrectos.",
                allowEscapeKey: false,
                alowOutsideClick: false
              });
            }
          } else if (this.usuario.rol == "Paciente") {
            let response = await this.$axios.get(url_apipaciente);
            let users = response.data;
            let findUser = users.find(x => {
              return (
                x.id === this.usuario.user &&
                x.id === this.usuario.password
              );
            });
            if (findUser) {
              delete findUser.password;
              localStorage.setItem("user-paciente", JSON.stringify(findUser));
              this.$router.push("Usuario/usuarioPerfil");
            } else {
              this.$swal.fire({
                type: "error",
                title: "Oops",
                text:
                  "El correo o la contraseña diligenciados son incorrectos.",
                allowEscapeKey: false,
                alowOutsideClick: false
              });
            }
          }
        } else {
          this.$swal.fire({
            type: "warning",
            title: "Formulario incompleto.",
            text: "Hay campos que deben ser diligenciados."
          });
        }
      } catch (error) {
        console.log(error);
        this.$swal.fire({
          type: "error",
          title: "Error al iniciar sesión.",
          text: ""
        });
      }
    },
    async loginadmin() {
      try {
        if (this.$refs.formAdmin.validate()) {
          //llamado a la api

          let response = await this.$axios.get(url_apiadmin);
          let admin = response.data;
          let findUser = admin.find(x => {
            return x.codigo === this.admin.codigo;
          });
          if (findUser) {
            localStorage.setItem("admin-system", JSON.stringify(findUser));
            this.$router.push("Administrador/adminUsuarios");
          } else {
            this.$swal.fire({
              type: "error",
              title: "Oops",
              text: "El codigo ingresado es incorrecto.",
              allowEscapeKey: false,
              alowOutsideClick: false
            });
          }
        } else {
          this.$swal.fire({
            type: "warning",
            title: "Formulario incompleto.",
            text: "Hay campos que deben ser diligenciados."
          });
        }
      } catch (error) {
        console.log(error);
        this.$swal.fire({
          type: "error",
          title: "Error al iniciar sesión.",
          text: ""
        });
      }
    }
  }
};
</script>
<style>
#app {
  background: url("../static/images/FondoIndex.png") no-repeat center fixed !important;
  background-size: cover;
}
</style>
