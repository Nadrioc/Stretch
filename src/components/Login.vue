<template>
  <div class="login-container">
    <transition appear name="fade-toast">
      <div v-if="invalidInput" class="toast">
        <div class="red-text tiny-text white-back">
          {{ errorContent }}
        </div>
      </div>
    </transition>
    <div class="form-container">
      <div class="login-label">
        <p class="red-text small-text">User Login</p>
      </div>
      <div>
        <p class="blue-text tiny-text input-label">Email</p>
        <input
          class="login-input full-input blue-text tiny-text"
          type="text"
          v-model="email"
        />
      </div>
      <div>
        <p class="blue-text tiny-text input-label">Password</p>
        <input
          class="login-input full-input blue-text tiny-text"
          type="password"
          v-model="password"
        />
      </div>
      <div class="login-label">
        <p @click="signIn" class="red-text hover-red small-text">Login</p>
      </div>

      <div class="login-label blue-text tiny-text">
        New to Stretch?
        <router-link :to="{ path: '/new-user' }">
          <span class="red-text hover-red sign-up-margin">Sign Up</span>
        </router-link>
      </div>
      <div class="login-label blue-text tiny-text">
        Or
        <span @click="anonSignin" class="red-text hover-red sign-up-margin"
          >Enter as a Guest</span
        >
      </div>
    </div>
    <app-circle></app-circle>
  </div>
</template>

<script>
import Circle from "./Circle.vue";
import firebase from "firebase/app";
import fireauth from "firebase/auth";
import * as auth from "../services/auth";
export default {
  data() {
    return {
      email: "",
      password: "",
      invalidInput: false,
      errorContent: null
    };
  },
  components: {
    "app-circle": Circle
  },
  methods: {
    signIn() {
      firebase
        .auth()
        .signInWithEmailAndPassword(this.email, this.password)
        .then(async (data) => {
          await auth.setUser(data.user.uid);
          this.$router.push({
            name: "dashboard",
            params: { update: "fromLanding" }
          });
        })
        .catch((error) => {
          if (error.code === "auth/user-not-found") {
            this.invalidInput = true;
            this.errorContent = "Your email or password was incorrect";
          } else if (error.code === "auth/invalid-email") {
            this.invalidInput = true;
            this.errorContent = "Invalid email";
          } else {
            this.invalidInput = true;
            this.errorContent = error.message;
          }
        });
    },
    anonSignin() {
      firebase
        .auth()
        .signInAnonymously()
        .then(async (data) => {
          await auth.setUser(data.user.uid);
          this.$router.push({
            name: "dashboard",
            params: { update: "fromLanding" }
          });
        })
        .catch((error) => {
          // Handle Errors here.
          console.log(error.message);
          // ...
        });
    }
  }
};
</script>

<style scoped>
.full-input {
  width: 100%;
}

.login-input {
  border-bottom: 3px solid #2ab7ca;
}

.login-label {
  display: flex;
  justify-content: center;
  margin-bottom: 10px;
  margin-top: 20px;
}

.input-label {
  margin-top: 20px;
}

.login-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
.form-container {
  color: white;
  width: 50%;
  height: 85%;
}

.sign-up-margin {
  margin-left: 10px;
}

@media screen and (min-width: 850px) {
  .full-input {
    width: 80%;
  }
}
</style>
