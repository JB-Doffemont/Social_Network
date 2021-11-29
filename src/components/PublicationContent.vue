<template>
  <div>
    <form @submit.prevent class="pubcontent_container">
      <h2>Partagez Maintenant</h2>
      <!-- l'utilisateur poste son propre article -->
      <!-- TITLE -->
      <label class="bottomMargin bold" for="title">Titre de post :</label>
      <input class="bottomMargin" id="title" type="text" v-model="title" />
      <!-- CONTENT -->
      <label class="bottomMargin bold" for="textarea"
        >Ecrivez votre post ici :</label
      >
      <textarea
        class="bottomMargin"
        name="textcontainer"
        id="textarea"
        v-model="content"
      ></textarea>
      <img :src="profilePicture" alt="" />
      <input type="file" @change="downloadImage" accept="/*" />

      <!-- BOUTON POSTER -->
      <button class="submitPost" @click="postPublication()">
        <img class="logo widthFifty" src="../assets/img/commenter.png" />
        <p class="pubContent bottomMargin widthFifty">Validez</p>
      </button>
    </form>
    <!-- Afficher les content du post -->
    <div v-if="success == true">
      <h2>Preview de votre post :</h2>
      <GetPublication
        :titlePost="this.title"
        :contentPost="this.content"
        :urlImage="this.profilePicture"
      >
      </GetPublication>
    </div>
  </div>
</template>

<script>
import GetPublication from "./GetPublication.vue";
export default {
  data() {
    return {
      title: "",
      content: "",
      success: false,
      profilePicture: "",
      remoteUrl: "",
    };
  },
  components: {
    GetPublication: GetPublication,
  },
  methods: {
    downloadImage(e) {
      const image = e.target.files[0];
      const reader = new FileReader();
      reader.readAsDataURL(image);
      reader.onload = (e) => {
        this.profilePicture = e.target.result;
        console.log(this.profilePicture);
      };
    },

    //Demande asynchronisée permettant d'afficher le poste de l'utilisateur et l'envoi des données saisies au serveur API
    async postPublication() {
      const url = "https://dw-s3-nice-facebox.osc-fr1.scalingo.io/post";
      //Options de la requête API
      const options = {
        method: "POST",
        headers: {
          Authorization: " Bearer " + localStorage.getItem(`@token`),
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          title: this.title,
          content: this.content,
          image: this.profilePicture,
        }),
      };
      // va chercher les options de l'API
      const response = await fetch(url, options);
      console.log(response);
      // la récupération des data en json
      const data = await response.json();
      console.log(data);

      this.success = true;
    },
  },
};
</script>
<style scoped>
.pubcontent_container {
  display: flex;
  flex-direction: column;
  margin: auto;
  padding: 70px 30px 20px 30px;
  width: 400px;
  background-color: #e0a102;
  margin: auto;
  border: 3px solid #403c39;
  border-radius: 4%;
  box-shadow: 2px 4px 2px #403c39;
}
.pubContent {
  margin: auto;
  width: 75px;
  background-color: #e9c872;
  border: none;
}
form h2 {
  margin-top: 0;
}
.submitPost {
  display: flex;
  flex-direction: row;
  justify-content: center;
  background-color: #f3f0e77d;
  width: 120px;
  margin: auto;
  border-radius: 5%;
  box-shadow: 2px 2px 2px black;
}
.logo {
  margin-left: 5px;
}
.widthFifty {
  width: 50px;
}
.bottomMargin {
  margin-bottom: 10px;
}
.bold {
  font-weight: 600;
}

@media (max-width: 800px) {
  .pubcontent_container {
    width: 245px;
    margin-left: 15%;
  }
}
</style>
