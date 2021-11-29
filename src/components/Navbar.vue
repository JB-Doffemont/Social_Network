<template>
  <div>
    <!-- Header - Logo et recherche -->
    <header>
      <label class="burger" for="toggle">☰</label>
      <input type="checkbox" id="toggle" />
      <div class="logoSimplif">
        <router-link to="/">
          <img src="../assets/img/f.svg" style="width: 50px" />
        </router-link>
      </div>
    </header>

    <!-- Navbar vertical -->
    <div class="nav_vert_container">
      <nav class="vertical_nav">
        <div class="btn_connect">
          <!-- PROFIL -->
          <router-link to="/profil"
            ><img
              src="../assets/img/user.svg"
              style="width: 50px"
            /><br />Profil</router-link
          >
          <!-- HOME -->
          <router-link to="/"
            ><img
              src="../assets/img/home.svg"
              style="width: 44px"
            /><br />Home</router-link
          >
          <!-- SHOP -->
          <router-link to="/shop"
            ><img
              src="../assets/img/boutique.svg"
              style="width: 49px"
            /><br />Shop</router-link
          >
          <!-- GROUPS -->
          <router-link to="/ProfilUser"
            ><img
              src="../assets/img/communauté.svg"
              style="width: 49px"
            /><br />Group</router-link
          >
        </div>
        <!-- Disconnect bouton -->
        <div class="btn_disconnect" v-if="token">
          <router-link to="/connexion" @click="clearLocalStorage"
            ><img src=../assets/img/deconnection.svg style= width:49px /><br />Deconnexion</router-link
          >
        </div>
        <div class="btn_connect" v-else>
          <router-link to="/connexion" @click="clearLocalStorage"
            ><img src=../assets/img/deconnection.svg style= width:49px /><br />Connexion</router-link
          >
        </div>
      </nav>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    post: Array,
    callback: Function,
    LastNameQuery: String,
  },

  data() {
    return {
      token: localStorage.getItem("@token"),
      searchQuery: "",
      postArray: this.post,
      lastNameData: this.LastNameQuery,
    };
  },

  methods: {
    //methods permettant la perte du token au clic du btn disconnect
    clearLocalStorage() {
      localStorage.clear();
    },
  },
};
</script>


<style scoped>
body {
  font-family: "Lato", sans-serif;
}
.burger,
#toggle {
  display: none;
}
.nav_vert_container {
  position: fixed;
  top: 60px;
  left: 0;
  bottom: 0;
}
.vertical_nav {
  width: 110%;
  height: 100%;
  background-color: #808080;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}
header {
  background-color: #403c39;
  height: 60px;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1;
}

.logoSimplif {
  position: absolute;
  left: 96px;
}

.rechercheNavBar input {
  width: 270px;
  color: #403c39;
  font-size: 15px;
}

.rechercheNavBar input:focus {
  outline: none;
  border-width: 3px;
  border-color: #e0a102;
  background-color: #f1f0f1;
  transition: 0.5s;
}

.btn_disconnect {
  align-items: flex-end;
  margin-bottom: 10px;
}

.btn_disconnect a {
  align-items: flex-end;
  text-decoration: none;
  color: #f1f0f1;
  text-shadow: 2px 2px 2px #403c39;
}

.btn_connect {
  display: flex;
  flex-direction: column;
}

.btn_connect a {
  margin-bottom: 28px;
  padding-top: 5px;
  text-decoration: none;
  color: #f1f0f1;
  text-shadow: 2px 2px 2px #403c39;
}

.btn_connect a:hover {
  color: #e0a102;
  transition: 0.8s;
}
a.router-link-exact-active {
  margin-bottom: 28px;
  padding-top: 5px;
  text-decoration: none;
  color: #e0a102;
  text-shadow: 2px 2px 2px #403c39;
}
@media (max-width: 800px) {
  .burger {
    display: flex;
    color: white;
    margin-right: 90%;
    font-size: 40px;
    height: 58px;
    cursor: pointer;
    justify-content: center;
    align-items: center;
    font-size: 40px;
  }

  .vertical_nav {
    display: none;
    flex-direction: column;
    width: 100px;
    height: 200px;
    background-color: #808080;
    justify-content: space-between;
  }

  #toggle:checked + .vertical_nav {
    display: flex;
  }
}
</style>
