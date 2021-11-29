<template>
  <div>
    <!--Appel le composant Navbar -->
    <Navbar></Navbar>
    <h1>Votre Profil</h1>
    <!-- Afficher le profil 
    -- Si editing n'est pas activé, les valeurs s'affichent
    -- Else les input s'affichent pour pouvoir modifier
    -->
    <div class="profil">
      <table>
        <tr>
          <th>First Name :</th>
          <td>
            <p v-if="!showEditProfil">{{ firstName }}</p>
            <input v-else type="text" id="firstName" v-model="firstName" />
          </td>
        </tr>
        <tr>
          <th>Last Name :</th>
          <td>
            <p v-if="!showEditProfil">{{ lastName }}</p>
            <input v-else type="text" id="lastName" v-model="lastName" />
          </td>
        </tr>
        <tr>
          <th>Email :</th>
          <td>
            <p v-if="!showEditProfil">{{ email }}</p>
            <input v-else type="email" id="email" v-model="email" />
          </td>
        </tr>
        <tr>
          <th>Age :</th>
          <td>
            <p v-if="!showEditProfil">{{ age }}</p>
            <input v-else type="number" id="age" v-model="age" />
          </td>
        </tr>
        <tr>
          <th>Job :</th>
          <td>
            <p v-if="!showEditProfil">{{ occupation }}</p>
            <input v-else type="text" id="occupation" v-model="occupation" />
          </td>
        </tr>
        <tr>
          <th>Password :</th>
          <td>
            <div class="buttonsPassword" v-if="!showEditProfil">
              <p>{{ password }}</p>
              <div v-if="!showEditProfil && token">
                <button v-if="shown == true" @click="showPassword()">
                  <img
                    src="../assets/img/eyeClosed.png"
                    style="width: 20px; height: 20px"
                  />
                </button>
                <button v-else @click="showPassword()">
                  <img
                    src="../assets/img/eyeOpen.png"
                    style="width: 20px; height: 20px"
                  />
                </button>
              </div>
            </div>
            <input v-else type="password" id="password" v-model="password" />
          </td>
        </tr>
      </table>
      <!--Appel de la method editProfil qui permet le changement de statut booléan
      permettant d'afficher et de masquer les champs de saisies pour modification du profil -->
      <div>
        <button v-if="!showEditProfil && token" @click="editProfil">
          Modifier
        </button>
        <!--Appel de la fonction PutProfil qui envoie les données au serveur  -->
        <button v-if="token && showEditProfil" @click="PutProfil()">
          Valider
        </button>
      </div>
      <!--Message d'alerte qui confirme à l'utilisateur qu'il a bien éffectué ses changements -->
      <p v-show="this.success == true && !showEditProfil" class="success">
        Votre profil a bien été modifié !
      </p>
    </div>

    <!--Attribution des valeurs aux props de "GetPublication.vue"/ 
    Récupération des élements dans le tableau de l'API -->
    <div class="ownPost">
      <GetPublication
        v-for="(element, index) in post"
        :key="index"
        :titlePost="element.title"
        :contentPost="element.content"
        :idPost="element._id"
        :commentsPost="element.comments"
        :firstname="element.firstname"
        :lastname="element.lastname"
        :likePost="element.likes.length"
        :urlImage="element.imageUrl"
      ></GetPublication>
    </div>
  </div>
</template>

<script>
// import composants
import Navbar from "../components/Navbar.vue";
import GetPublication from "../components/GetPublication.vue";

export default {
  //Création des data properties
  data() {
    return {
      // data du profil déjà stocké
      firstName: "",
      lastName: "",
      email: "",
      age: 0,
      occupation: "",
      password: "",
      // toggle entre password caché ou non caché
      shown: true,
      value: "",
      // boolean pour afficher modifier form
      showEditProfil: false,
      // deviens true si modification est effectué
      successEdited: false,
      success: "",
      // array pour les posts
      post: [],
      token: localStorage.getItem("@token"),
      idUser: "",
    };
  },
  // récupération des components
  components: {
    Navbar: Navbar,
    GetPublication: GetPublication,
  },

  //Création de mounted qui permet l'affichage au montage de la page de façon asynchronisée
  async mounted() {
    const urlGetProfil = "https://dw-s3-nice-facebox.osc-fr1.scalingo.io/user";
    //Options de la requête API
    const optionGetProfil = {
      method: "GET",
      headers: {
        //récupération du token pour vérifier que l'utilisateur existe bien et puisse accéder a son profil
        Authorization: " bearer " + localStorage.getItem(`@token`),
        "content-type": "application/json",
      },
    };
    // création de la const de réponse qui va chercher les options de l'API
    const responseGetProfil = await fetch(urlGetProfil, optionGetProfil);
    console.log(responseGetProfil);

    // Création de la const data qui nous permet la récupération des data stockées dans l'API
    const dataGetProfil = await responseGetProfil.json();
    console.log(dataGetProfil);

    //Récupération des data stockées par l'API
    this.firstName = dataGetProfil.firstname;
    this.lastName = dataGetProfil.lastname;
    this.email = dataGetProfil.email;
    this.age = dataGetProfil.age;
    this.occupation = dataGetProfil.occupation;
    this.password = dataGetProfil.password;
    this.idUser = dataGetProfil._id;
    console.log("ceci est un userid", this.idUser);

    const urlGetPost =
      "https://dw-s3-nice-facebox.osc-fr1.scalingo.io/posts?limit=10000";
    //Options de la requête API
    const optionGetPost = {
      method: "GET",
      headers: {
        "Content-Type": "application/json",
      },
    };
    // création de la const de réponse qui va chercher les options de l'API
    const responseGetPost = await fetch(urlGetPost, optionGetPost);
    console.log(responseGetPost);

    // Création de la const data qui nous permet la récupération des data stockées dans l'API
    const dataGetPost = await responseGetPost.json();
    console.log(dataGetPost);

    // attribution des elements présent dans l'API à notre data qui est initialement un tableau vide
    this.post = dataGetPost.posts.filter((element) => {
      return element.userId === this.idUser;
    });
  },
  //Création de la method permmettant de modifier le profil
  methods: {
    async PutProfil() {
      const urlPutProfil =
        "https://dw-s3-nice-facebox.osc-fr1.scalingo.io/user";
      //Options de la requête API
      const optionPutProfil = {
        method: "PUT",

        headers: {
          Authorization: " bearer " + localStorage.getItem(`@token`),
          "content-type": "application/json",
        },
        // Ne pas oublier de stringify
        body: JSON.stringify({
          firstname: this.firstName,
          lastname: this.lastName,
          email: this.email,
          age: this.age,
          occupation: this.occupation,
          password: this.password,
        }),
      };
      // création de la const de réponse qui va chercher les options de l'API
      const response = await fetch(urlPutProfil, optionPutProfil);
      console.log(response);

      // Création de la const data qui nous permet la récupération des data stockées dans l'API
      const dataPutProfil = await response.json();
      console.log(dataPutProfil);
      // Récupération de la data success afin de créer le message de confirmation de modification du profil
      this.success = dataPutProfil.success;

      //Modification des valeurs du profil avec les nouvelles valeurs des inputs grace a la data success
      if (this.success == true) {
        // on va cacher les input quand on a sauvegardé
        this.showEditProfil = false;
      }
    },
    //methods dans le but d'afficher et masquer les inputs de modification
    editProfil() {
      this.showEditProfil = !this.showEditProfil;
    },
    // method pour montrer et cacher le mot de passe
    showPassword() {
      if (this.shown == true) {
        this.value = this.password;
        this.password = "*".repeat(this.value.length);
      } else {
        this.password = this.value;
      }
      this.shown = !this.shown;
    },
  },
};
</script>

<style scoped>
* {
  font-family: "Lato", sans-serif;
  margin: 0;
}
h1 {
  z-index: 311;
  position: fixed;
  color: white;
  top: 0;
  left: 44%;
  margin-top: 10px;
}
/** Profile section */
.profil {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-evenly;
  background: #e0a1026b;
  height: 400px;
  padding-top: 70px;
  margin: auto;
  margin-bottom: 30px;
}

.profil input {
  width: 80%;
  border-radius: 5px;
  margin: 10px auto;
  padding: 5px;
  color: #e0a102;
  font-size: 18px;
  font-weight: 900;
}

.profil p {
  text-align: left;
  margin: 5px 30px;
}

.profil th {
  width: 100px;
}

.profil button {
  margin: 10px auto;
  width: 150%;
  padding: 12px;
  cursor: pointer;
  background-color: #403c39;
  color: #f1f0f1;
  border: 3px solid #e0a102;
  border-radius: 5px;
}

.profil button:hover {
  cursor: pointer;
  transition: all 0.5s ease-in;
  background-color: #e0a102;
  border-radius: 5px;
  border: 3px solid #403c39;
  color: #403c39;
}

table {
  width: 250px;
  text-align: justify;
}
table p {
  margin: 0;
  font-size: 18px;
  font-weight: 900;
}
.buttonsPassword {
  display: flex;
  flex-direction: row;
  align-items: center;
}
.buttonsPassword button {
  width: 60px;
  font-size: x-small;
}
.success {
  font-size: large;
  font-weight: 500;
  color: #403c39;
  border: 4px dashed #e0a102;
  padding: 2px;
}

@media (max-width: 800px) {
  .profil {
    width: 250px;
    margin-left: 23%;
  }
}
</style>