<template>
  <div class="container">
    <div class="registration_container">
      <!--Logo et text container-->
      <div class="leftContain">
        <img src="../assets/img/facebox.svg" />
        <p class="addvertissementFacebox">Si tu te sens FAT</p>
        <p class="addvertissementFacebox">et que tu veux de venir FIT</p>
        <p class="addvertissementFacebox">
          Rejoins notre communauté de CROSSFIT
        </p>
      </div>
      <!--Form inscription container -->
      <div class="rightContain">
        <h2>Registration</h2>
        <!--en submit appel de la fontion qui crée un compte utilisateur 
        et qui envoie les données au serveur -->
        <!--v-model sur les inputs afin de récupérer les valeurs 
        saisis par l'utilisateur -->
        <form
          class="registration"
          @submit.prevent="[CreateAccount(), accessConnexion()]"
        >
          <!-- LAST NAME -->
          <label for="lastName">First Name : </label>
          <input type="text" id="lastName" v-model="inputLastName" />
          <!-- FIRST NAME -->
          <label for="firstName">Last Name : </label>
          <input type="text" id="firstName" v-model="inputFirstName" />
          <!-- EMAIL -->
          <label for="email">E-mail : </label>
          <input type="text" id="email" v-model="inputEmail" />
          <!-- PASSWORD -->
          <label for="password">Password : </label>
          <div style="width: 314px">
            <input
              v-if="this.show == false"
              id="passwordOne"
              type="password"
              v-model="inputPassword"
            />
            <input
              v-else
              id="passwordOne"
              type="text"
              v-model="inputPassword"
            />
            <button type="button" class="pwButton" @click="showPassword()">
              <img
                v-if="this.show == true"
                src="../assets/img/eyeClosed.png"
                class="eye"
              />
              <img v-else src="../assets/img/eyeOpen.png" class="eye" />
            </button>
          </div>
          <!--création d'un v-if si les champs ne sont pas tous remplis -->
          <p class="p_wrong_email" v-if="this.success == false">
            Veuillez remplir les champs
          </p>
          <!-- BOUTON VALIDER -->
          <input id="button_inscription" type="submit" value="Inscription" />
        </form>
      </div>
    </div>

    <!--routes qui permettent la navigation-->
    <div>
      <router-link to="/">Home</router-link> |
      <router-link to="/connexion">Connexion</router-link>
    </div>
  </div>
</template>

<script>
export default {
  //Création des data properties
  data() {
    return {
      // input valeurs
      inputLastName: "",
      inputFirstName: "",
      inputEmail: "",
      inputPassword: "",
      // success true si l'utilisateur est enregistré
      success: true,
      show: false,
    };
  },
  //Création de la méthode
  methods: {
    //Demande asynchronisée permettant l'inscription de l'utilisateur et l'envoi des données saisies au serveur API
    async CreateAccount() {
      const url = "https://dw-s3-nice-facebox.osc-fr1.scalingo.io/register";
      //Options de la requête API
      const options = {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        // Ne pas oublier de stringify
        body: JSON.stringify({
          firstname: this.inputFirstName,
          lastname: this.inputLastName,
          email: this.inputEmail,
          password: this.inputPassword,
        }),
      };
      // va chercher les options de l'API
      const response = await fetch(url, options);
      console.log(response);
      // la récupération des data stockées dans l'API
      const data = await response.json();
      console.log(data);
      //Récupération du booléan success généré par l'API afin d'indiqué à l'utilisateur qu'il a bien rempli tous les champs de saisis
      this.success = data.success;
      // route à connexion si c'est un succès
      if (this.success == true) {
        this.$router.push("/connexion");
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

<style >
* {
  font-family: "Lato", sans-serif;
}

#passwordOne {
  margin-left: 10px;
}
.container {
  display: flex;
  flex-direction: column;
}
.registration {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.rightContain h2 {
  margin-left: 25px;
}

.rightContain h2::after {
  content: "";
  display: block;
  width: 25%;
  border: 1px solid #e0a102;
  position: relative;
  bottom: 14px;
}

.registration_container {
  display: flex;
  width: 1000px;
  height: 700px;
  margin: auto 0 auto 10px;
}

.leftContain {
  display: flex;
  flex-direction: column;
  justify-content: center;
  width: 600px;
  height: 700px;
  padding-right: 120px;
  font-family: "Lato", sans-serif;
  font-size: 27px;
  font-weight: 700;
  color: #403c39;
}

.rightContain {
  display: flex;
  flex-direction: column;
  justify-content: center;
  width: 400px;
  height: 700px;
}

.registration_container input {
  width: 80%;
  border-radius: 5px;
  margin: 10px auto;
  padding: 8px;
  color: #e0a102;
  font-size: 18px;
  font-weight: 900;
}

.registration_container label {
  text-align: left;
  margin: 5px 30px;
}

#button_inscription {
  margin: 10px auto;
  width: 40%;
  padding: 12px;
  cursor: pointer;
  background-color: #403c39;
  color: #f1f0f1;
  border: 3px solid #e0a102;
}
.eye {
  width: 20px;
  height: 20px;
  border: none;
  background-color: none;
}

#button_inscription:hover {
  cursor: pointer;
  transition: all 0.5s ease-in;
  background-color: #e0a102;
  border-radius: 5px;
  border: 3px solid #403c39;
  color: #403c39;
}
</style>
