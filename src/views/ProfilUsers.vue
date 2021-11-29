<template>
  <div>
    <!-- Appeler le composant navbar -->
    <Navbar></Navbar>
    <!-- Afficher le profil de l'utilisateur -->
    <div class="profilUser">
      <p>First Name : {{ this.firstname }}</p>
      <p>Last Name : {{ this.lastname }}</p>
      <p>E-mail : {{ this.email }}</p>
      <p>Age : {{ this.age }}</p>
      <p>Occupation : {{ this.occupation }}</p>
    </div>
  </div>
</template>


<script>
// Import navbar
import Navbar from "../components/Navbar.vue";
export default {
  props: {
    userId: String,
  },
  data() {
    return {
      // data du profil
      age: "",
      email: "",
      lastname: "",
      firstname: "",
      occupation: "",
      // l'id de l'utilisateur
      id: "",
    };
  },
  components: {
    Navbar: Navbar,
  },

  // méthode mounted pour trouver l'id et afficher le profil - avec local storage
  async mounted() {
    const url = `https://dw-s3-nice-facebox.osc-fr1.scalingo.io/user/${this.userId}`;
    const options = {
      method: "GET",
      headers: {
        "Content-Type": "application/json",
        // authorise avec le token stocké en local storage
        Authorization: " Bearer " + localStorage.getItem(`@token`),
      },
    };
    // fetch la réponse
    const response = await fetch(url, options);
    console.log(response);
    // parse la réponse en JSON
    const data = await response.json();
    console.log(data);
    // afficher les détails trouvé
    this.age = data.age;
    this.email = data.email;
    this.lastname = data.lastname;
    this.firstname = data.firstname;
    this.occupation = data.occupation;
  },
};
</script>
<style>
.profilUser {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-evenly;
  background: #e0a1026b;
  height: 380px;
  padding-top: 50px;
  margin: auto;
  margin-bottom: 30p;
  margin: 0;
  font-size: 18px;
  font-weight: 900;
}

.profilUser p {
  text-align: left;
  margin: 5px 30px;
}
</style>