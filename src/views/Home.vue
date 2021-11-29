<template>
  <div class="home">
    <!--Appel des composants -->

    <Navbar> </Navbar>
    <input
      type="text"
      v-model="searchQuery"
      class="inputQuery"
      placeholder="Recherche..."
    />
    <PublicationContent></PublicationContent>
    <!--Attribution des valeurs aux props de "GetPublication.vue"/ 
    Récupération des élements dans le tableau de l'API -->

    <h2>Inspirez-vous maintenant :</h2>
    <GetPublication
      v-for="(element, index) in resultQuery"
      :key="index"
      :titlePost="element.title"
      :contentPost="element.content"
      :idPost="element._id"
      :commentsPost="element.comments"
      :firstname="element.firstname"
      :lastname="element.lastname"
      :likePost="element.likes.length"
      :userId="element.userId"
      :dislikePost="element.dislikes.length"
      :urlImage="element.imageUrl"
    >
    </GetPublication>
  </div>
</template>

<script>
//Import des différents components
import Navbar from "../components/Navbar.vue";
import PublicationContent from "../components/PublicationContent.vue";
import GetPublication from "../components/GetPublication.vue";

export default {
  //Création des datas properties

  data() {
    return {
      // post array pour afficher plusieurs articles
      post: [],
      lastname: "",
      searchQuery: "",
    };
  },
  // récupération des components
  components: {
    Navbar: Navbar,
    PublicationContent: PublicationContent,
    GetPublication: GetPublication,
  },
  //Création de mounted qui permet l'affichage au montage de la page de façon asynchronisée des posts utilisateurs
  async mounted() {
    const url =
      "https://dw-s3-nice-facebox.osc-fr1.scalingo.io/posts?limit=10000";
    //Options de la requête API
    const options = {
      method: "GET",
      headers: {
        "Content-Type": "application/json",
      },
    };
    // création de la const de réponse qui va chercher les options de l'API
    const reponse = await fetch(url, options);
    console.log(reponse);
    // Création de la const data qui nous permet la récupération des data stockées dans l'API
    const data = await reponse.json();
    console.log(data);

    // attribution des elements présent dans l'API à notre data qui est initialement un tableau vide
    this.post = data.posts;
    this.lastname = this.post.lastname;
    console.log(this.post);
    this.firstname = this.post.firstname;
    this.content = this.post.content;
  },
  computed: {
    resultQuery() {
      console.log("hola", this.post);
      return this.post.filter((element) => {
        return (
          element.lastname
            .toLowerCase()
            .includes(this.searchQuery.toLowerCase()) ||
          element.firstname
            .toLowerCase()
            .includes(this.searchQuery.toLowerCase()) ||
          element.content
            .toLowerCase()
            .includes(this.searchQuery.toLowerCase()) ||
          element.title.toLowerCase().includes(this.searchQuery.toLowerCase())
        );
      });
    },
  },
};
</script>
<style>
.inputQuery {
  z-index: 311;
  position: fixed;
  top: 3%;
  left: 44%;
}

body {
  margin: 0;
}
</style>
