<template>
  <!-- POST -->
  <div class="post_container">
    <!-- Nom de l'auteur avec lien vers son profil -->
    <div class="publicationPost user_name">
      <div @click="navigateToUserProfile" class="profilPost">
        <img src="../assets/img/user.svg" style="width: 50px" />
        <h3>{{ firstname }} {{ lastname }}</h3>
      </div>
    </div>
    <!-- Affichage de l'article -->
    <div class="mid_container">
      <!-- L'image -->
      <div class="img_container">
        <img :src="this.ImageUrl" />
      </div>
      <!-- Le poste avec titre, content et commentaires -->
      <div class="content_container">
        <!-- TITRE -->
        <h4 class="titlePost">{{ titlePost }}</h4>
        <!-- CONTENT -->
        <div class="p_container">
          <p class="contentPost">{{ contentPost }}</p>
        </div>
        <!-- COMMENTAIRES -->
        <h4 class="commentaireTitre">Commentaires :</h4>
        <ul class="comment_container">
          <li v-for="(element, index) in arrayComment" :key="index">
            <p>{{ element }}</p>
          </li>
          <li
            class="commentIndiv"
            v-for="(element, index) in commentsPost"
            :key="index"
          >
            <p class="nameComment">
              <b>{{ element.lastname }} :</b>
            </p>
            <p>{{ element.content }}</p>
          </li>
        </ul>
      </div>
    </div>
    <!-- POST -->

    <!--COMMENT/LIKE-->
    <div class="likes_container" v-if="token">
      <div class="like_content">
        <!-- Fit like bouton -->
        <button @click="PostLike">
          <img src="../assets/img/muscle-du-bras.png" />
          Fit
        </button>
        <p>{{ this.like }}</p>
      </div>
      <div class="dislike_content">
        <!-- Fat like bouton -->
        <button @click="PostDislike">
          <img src="../assets/img/muscle-du-bras.png" />
          Fat
        </button>
        <p>{{ this.dislike }}</p>
      </div>
      <div class="comment_content">
        <!-- Bouton pour écrire un commentaire -->
        <button class="btnComment" @click="showAddComment">
          <img src="../assets/img/commenter.png" />
          Commenter
        </button>
      </div>
    </div>
    <!--COMMENT/LIKE-->

    <!--POST COMMENT-->
    <form
      @submit.prevent="[PostComment(), addComment()]"
      class="show_comment"
      v-show="showComment"
    >
      <input
        type="text"
        v-model="inputComment"
        placeholder="votre commentaire..."
        class="inputType"
      />
      <input class="addCommentButton" type="submit" value="Valider" />
    </form>
    <!--POST COMMENT-->
  </div>
</template>

<script>
export default {
  props: {
    titlePost: String,
    contentPost: String,
    idPost: String,
    commentsPost: Array,
    firstname: String,
    lastname: String,
    likePost: Number,
    userId: String,
    dislikePost: Number,
    urlImage: String,
  },
  data() {
    return {
      arrayComment: [],
      inputComment: "",
      success: "",
      showComment: false,
      like: this.likePost,
      getId: this.userId,
      token: localStorage.getItem("@token"),
      dislike: this.dislikePost,
      ImageUrl: this.urlImage,
    };
  },
  methods: {
    /* Demande asynchronisée permettant l'utilisateur d'ajouter
     * un commentaire s'ils sont connecté
     */
    async PostComment() {
      const url = "https://dw-s3-nice-facebox.osc-fr1.scalingo.io/post/comment";
      //Options de la requête API
      const options = {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          Authorization: " Bearer " + localStorage.getItem(`@token`),
        },
        // stringify ID et commentaire
        body: JSON.stringify({
          postId: this.idPost,
          content: this.inputComment,
        }),
      };
      // va chercher les options de l'API
      const response = await fetch(url, options);
      console.log(response);
      // la récupération des data stockées dans l'API
      const data = await response.json();
      console.log(data);
      this.success = data.success;
    },

    /** méthode à ajouter commentaire dans
     * l'array arrayComment
     */
    addComment() {
      this.arrayComment.push(this.inputComment);
      this.inputComment = "";
    },
    /** méthode pour pouvoir ajouter un commentaire en
     * click
     */
    showAddComment() {
      this.showComment = !this.showComment;
    },
    /** méthode asynchrone pour aimer un post  */
    async PostLike() {
      const url = "https://dw-s3-nice-facebox.osc-fr1.scalingo.io/post/like";
      const options = {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          //récupération du token pour vérifier que l'utilisateur existe bien et puisse accéder a son profil
          Authorization: " Bearer " + localStorage.getItem(`@token`),
        },
        body: JSON.stringify({
          postId: this.idPost,
        }),
      };
      // va chercher les options de l'API
      const response = await fetch(url, options);
      console.log(response);
      // la récupération en json des data stockées dans l'API
      const data = await response.json();
      console.log(data);
      if (data.success) {
        this.like++;
      }
    },
    async PostDislike() {
      const url = "https://dw-s3-nice-facebox.osc-fr1.scalingo.io/post/dislike";
      const options = {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          //récupération du token pour vérifier que l'utilisateur existe bien et puisse accéder a son profil
          Authorization: " Bearer " + localStorage.getItem(`@token`),
        },
        body: JSON.stringify({
          postId: this.idPost,
        }),
      };
      // va chercher les options de l'API
      const response = await fetch(url, options);
      console.log(response);
      // la récupération en json des data stockées dans l'API
      const data = await response.json();
      console.log(data);
      if (data.success) {
        this.dislike++;
      }
    },
    /** méthode asynchrone pour naviguer vers le profil d'un
     * utilisateur spécifique en remplacant l'url avec leur ID
     */
    async navigateToUserProfile() {
      this.$router.replace({
        name: "ProfilUser",
        params: { userId: this.getId },
      });
    },
  },
};
</script>

<style scoped>
.getpub_container {
  margin: 50px 0;
  padding: 30px 0;
}

.post_container {
  background: #f1f0f1;
  width: 820px;
  padding-bottom: 1px;
  margin: auto;
  border-top-right-radius: 30px;
  border-top-left-radius: 30px;
  margin-bottom: 30px;
  box-shadow: 1px 1px 2px #d3d3d3;
  white-space: pre-wrap;
}
/* Name Container */
.profilPost {
  display: flex;
  align-items: center;
  cursor: pointer;
}
.profilPost img {
  margin-left: 12px;
}
.titlePost {
  overflow-wrap: break-word;
}

.contentPost {
  overflow-wrap: break-word;
}
/*User Name*/
.user_name {
  background: #403c39;
  width: 100%;
  height: 60px;
  border-top-right-radius: 30px;
  border-top-left-radius: 30px;
}
.user_name h3 {
  color: #e0a102;
  text-decoration: none;
  text-align: left;
  padding: 2px 50px;
  text-transform: uppercase;
}
.user_name a {
  text-decoration: none;
}

/*Mid container */
.mid_container {
  display: flex;
}
/*Right Container */
.content_container {
  background: #d3d3d3;
  width: 40%;
  margin-top: 10px;
  margin-right: 10px;
  height: 309.5px;
}
.p_container p {
  margin: 5px;
}
.p_container {
  text-align: justify;
  overflow-y: auto;
  height: 36%;
  margin-top: 0;
}
.commentaireTitre {
  border-top: 2px solid white;
}
.commentIndiv {
  display: flex;
  flex-direction: row;
  justify-content: center;
  height: 30px;
  align-items: center;
  margin-top: 0;
  overflow-wrap: break-word;
}
.nameComment {
  padding-right: 5px;
}

/* Scroll */
.p_container::-webkit-scrollbar {
  width: 10px;
  background-color: #e0a102;
  border-radius: 30px;
}
.p_container::-webkit-scrollbar-track {
  box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
  border-radius: 30px;
}
.p_container::-webkit-scrollbar-thumb {
  border-radius: 30px;
  box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.5);
  background: #403c39;
}
.content_container h4 {
  margin-top: 5px;
  margin-bottom: 10px;
}
.comment_container li {
  list-style: none;
}
.comment_container {
  height: 38%;
  overflow-y: auto;
}
.comment_container::-webkit-scrollbar {
  width: 10px;
  background-color: #e0a102;
  border-radius: 30px;
}
.comment_container::-webkit-scrollbar-track {
  box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
  border-radius: 30px;
}
.comment_container::-webkit-scrollbar-thumb {
  border-radius: 30px;
  box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.5);
  background: #403c39;
}

.comment_content {
  display: flex;
  align-items: center;
}

.comment_content img {
  width: 36px;
  margin-right: 5px;
}
/* IMAGE */
.img_container {
  width: 60%;
}
.img_container img {
  width: 454px;
  margin-top: 10px;
  height: 310px;
}
/* Likes Container */
.likes_container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  background: #e0a102;
  height: 50px;
  margin: 10px 10px 30px 10px;
}
.like_content {
  display: flex;
  margin: 0 0 0 20px;
}
.like_content p {
  margin: 0;
  padding-top: 11%;
  color: #f1f0f1;
}

.dislike_content {
  display: flex;
  margin: 0 0 0 20px;
}
.dislike_content p {
  margin: 0;
  padding-top: 11%;
  color: #f1f0f1;
}

.likes_container button {
  display: flex;
  align-items: center;
  height: 40px;
  margin-right: 10px;
  border: none;
  background: none;
}

.likes_container button:hover {
  color: #f1f0f1;
  transition: 0.4s;
  cursor: pointer;
}

.like_content button img {
  width: 36px;
  padding-right: 5px;
}

.dislike_content button img {
  width: 36px;
  padding-left: 5px;
  transform: rotate(180deg);
}
/*Commentaire */
.show_comment {
  width: 100%;
  padding-bottom: 25px;
}

.show_comment .inputType {
  width: 84%;
  height: 40px;
  border-radius: 10px;
  background-color: #d3d3d3;
  cursor: pointer;
  border: white;
  outline: none;
  box-shadow: 1px 1px 2px #403c3979;
}

.show_comment input:focus {
  background-color: hsla(0, 0%, 83%, 0.489);
  transition: 0.6s;
}
/* Bouton add Commentaire */
.addCommentButton {
  margin-left: 22px;
}
.btnComment:hover {
  color: #f1f0f1;
  transition: 0.4s;
  cursor: pointer;
}

@media (max-width: 800px) {
  h2 {
    font-size: 15px;
  }

  .post_container {
    width: 290px;
    margin-left: 18%;
  }

  .mid_container {
    display: flex;
    flex-direction: column;
  }

  .content_container {
    width: 100%;
  }
  .img_container img {
    width: 290px;
  }

  .like_content {
    margin: 0px;
  }
  .likes_container {
    margin: 0;
  }
  .likes_container button img {
    width: 25px;
  }
}
</style>
