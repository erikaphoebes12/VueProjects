<template>
  <div>
    <the-header />
    <div class="w-full flex">
      <router-view></router-view>
    </div>
    <teleport to="body">
      <login-modal />
    </teleport>
  </div>
</template>

<script>
import TheHeader from "./components/TheHeader";
import LoginModal from "./components/Login/LoginModal";
import firebase from "./utilities/firebase";

export default {
  name: "App",
  mounted() {
    firebase.auth().onAuthStateChanged(user => {
      if (user) {
        // User is signed in.

        // Using Vuex State Management
        this.$store.commit("setIsLoggedIn", true);
        this.$store.commit("setAuthUser", user);

        // same with Vuex 4
        /* this.isLoggedIn = true;
    this.authUser = user; */
      } else {
        // No user is signed in.

        // Using Vuex State Management
        this.$store.commit("setIsLoggedIn", false);
        this.$store.commit("setAuthUser", {});

        // same with Vuex 4
        /* this.isLoggedIn = false;
    this.authUser = {}; */
      }
    });
  },
  components: {
    TheHeader,
    LoginModal
  }
};
</script>
