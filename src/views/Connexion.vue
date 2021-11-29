<template>
  <div>
    <div class="connexion">
      <!-- Logo -->
      <img
        class="faceboxLogo"
        src="../assets/img/facebox.svg"
        alt="logo Facebox"
      />
    </div>
    <div class="form_connexion_container">
      <!--appel de la fontion qui vérifie que l'utilisateur est bien inscrit au click et au keypress -->
      <!--v-model sur les inputs afin de récupérer les valeurs saisis par l'utilisateur -->
      <form @submit.prevent="connectUser()" class="form_connexion">
        <!-- EMAIL -->
        <label for="username">E-mail</label>
        <input id="username" type="email" v-model="email" />
        <!-- PASSWORD -->
        <label for="password">Password</label>
        <div style="width: 320px">
          <input
            v-if="this.show == false"
            id="password"
            type="password"
            v-model="password"
          />

          <input v-else id="password" type="text" v-model="password" />
          <button type="button" class="pwButton" @click="showPassword()">
            <img
              v-if="this.show == true"
              src="../assets/img/eyeClosed.png"
              style="width: 20px; height: 20px"
            />
            <img
              v-else
              src="../assets/img/eyeOpen.png"
              style="width: 20px; height: 20px"
            />
          </button>
        </div>

        <!-- FORGOTTEN PASSWORD - à faire! -->
        <router-link to="">Forgot your password ? </router-link>
        <!--création d'un v-if si les identifiants ne sont pas respectés -->
        <p class="p_wrong_email" v-if="this.success == false">
          wrong password or email
        </p>
        <!-- CONNECTION BOUTON -->
        <input id="btn_connexion" type="submit" value="Connexion" />
      </form>
    </div>
    <!--routes qui permettent la navigation-->
    <router-link to="/">Home</router-link> |
    <router-link to="/inscription">Inscription</router-link>
  </div>
</template>

<script>
export default {
  //Création des data properties
  data() {
    return {
      email: "",
      password: "",
      type: "password",
      success: true,
      show: false,
    };
  },
  //Création de la méthode
  methods: {
    //Demande asynchronisée permettant la récupération des identifiants utilisateur via l'API
    async connectUser() {
      const url = "https://dw-s3-nice-facebox.osc-fr1.scalingo.io/login";
      //Options de la requête API
      const options = {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        // Ne pas oublier de stringify
        body: JSON.stringify({
          email: this.email,
          password: this.password,
        }),
      };
      // création de la const de réponse qui va chercher les options de l'API
      const response = await fetch(url, options);
      console.log(response);
      // Création de la const data qui nous permet la récupération des data stockées dans l'API
      const data = await response.json();
      console.log(data);
      // Sauvegarde du token généré par l'API lors de la connection
      localStorage.setItem("@token", data.token);
      //Récupération du booléan success généré par l'API afin d'indiqué à l'utilisateur si les identifiants ne sont pas respectés
      this.success = data.success;
      console.log(this.success);
      if (this.success == true) {
        this.$router.push("/");
      }
    },
    showPassword() {
      if (this.show == false) {
        this.show = true;
      } else {
        this.show = false;
      }
    },
  },
};
</script>

<style>
* {
  font-family: "Lato", sans-serif;
}
.form_connexion {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.form_connexion_container {
  width: 400px;
  height: 462px;
  margin: auto;
}
.form_connexion_container input {
  padding: 10px 25px;
  border-radius: 5px;
  margin: 20px auto;
  padding: 8px;
  color: #e0a102;
  font-size: 18px;
  font-weight: 900;
}
.form_connexion input:hover {
  outline: none;
  border-width: 2px;
  border-color: #e0a102;
  transition: 0.5s;
}

.form_connexion input:focus {
  outline: none;
  border-width: 2px;
  border-color: #403c39 #e0a102 #403c39 #e0a102;
  background-color: #f1f0f176;
}

#btn_connexion {
  margin: 10px auto;
  width: 40%;
  padding: 12px;
  cursor: pointer;
  background-color: #403c39;
  color: #f1f0f1;
  border: 3px solid #e0a102;
}

#btn_connexion:hover {
  cursor: pointer;
  transition: all 0.5s ease-in;
  background-color: #e0a102;
  border-radius: 5px;
  border: 3px solid #403c39;
  color: #403c39;
}

.form_connexion_container label {
  text-align: left;
  margin: 5px 30px;
}

.pwButton {
  background-color: white;
  border: none;
}

.faceboxLogo {
  width: 345px;
  margin-bottom: 70px;
}

.p_wrong_email {
  color: red;
}

#password {
  margin-left: 30px;
}
</style>
