<template>
  <div class="auth">

    <template v-if="!user">
      <button @click="signInWithGoogle"> Sign in with Google </button>
    </template>
    <template v-if="user">
      
      <img class="avatar" :src="user.photoURL" alt="avatar" referrerpolicy="no-referrer">
      <!-- google is real paranoid about hotlinking images I guess the referrerpolicy seems to fix it
        https://stackoverflow.com/questions/40570117/http403-forbidden-error-when-trying-to-load-img-src-with-google-profile-pic -->
      <button @click="signOut">Sign Out</button>
    </template>

    <!-- <pre>{{ user }}</pre> -->
  </div>
</template>

<script>
import { auth, provider } from "@/firebaseConfig";
import { onAuthStateChanged, signInWithRedirect, signOut } from "firebase/auth";

export default {
    components: {},
    name: "LoginButton",
  data() {
    return {
      user: null
    };
  },
  beforeCreate: function() {
    onAuthStateChanged(auth, user => {
       console.log("user state:", user);
      // uncomment above to check out which user properties are available.
      if (user) {        
        this.user = user;
      } else {
        this.user = null;
      }
    });
  },
  methods: {
    signInWithGoogle: function() {
      signInWithRedirect(auth, provider)
        .then(result => {
          this.user = result.user;
        })
        .catch(err => console.log(err));
    },
    signOut: function() {
      signOut(auth)
        .then(() => {
          this.user = null;
          
        })
        .catch(err => console.log(err));
    }
  }
};
</script>

<style scoped>
.avatar {
  width: 2em; 
  height: 2em; 
  border-radius: 50%;
}
</style>
