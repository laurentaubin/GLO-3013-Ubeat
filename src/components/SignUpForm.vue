<template>
  <v-container class="main-container text-center">
    <p class="title">Sign up using email</p>

    <v-container>
      <v-row class="py-0">
        <v-col class="py-0">
          <v-text-field
            v-model="name"
            label="Name"
            :error="invalidName"
          ></v-text-field>
        </v-col>
      </v-row>

      <v-text-field
        class="py-0"
        v-model="email"
        label="Email"
        :error="invalidEmail"
      ></v-text-field>

      <v-row class="py-0">
        <v-col class="py-0">
          <v-text-field
            :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
            :type="showPassword ? 'text' : 'password'"
            v-model="password"
            label="Password"
            @click:append="showPassword = !showPassword"
            :error="invalidPassword"
          ></v-text-field>
        </v-col>
        <v-col class="py-0">
          <v-text-field
            :append-icon="showConfirmed ? 'mdi-eye' : 'mdi-eye-off'"
            :type="showConfirmed ? 'text' : 'password'"
            v-model="confirmedPassword"
            label="Confirm"
            @click:append="showConfirmed = !showConfirmed"
            :error="invalidPassword"
          >
          </v-text-field>
        </v-col>
      </v-row>

      <v-container>
        <v-btn @click="signUpFunction">
          Sign up
        </v-btn>
      </v-container>
    </v-container>

    <v-container>
      <p>
        Already have an account ?
        <a class="link white--text text-center text-md-start" href="/#/login"
          >Log in</a
        >
      </p>
    </v-container>
    <v-btn v-on:click="signUpFunction">
      click me
    </v-btn>
  </v-container>
</template>

<script>
import { signUp } from "../api/api";
import Router from "../router/index";

export default {
  name: "SignUpForm",
  data: function() {
    return {
      name: "",
      email: "",
      password: "",
      confirmedPassword: "",
      invalidName: false,
      invalidEmail: false,
      invalidPassword: false,
      showPassword: false,
      showConfirmed: false,
      emailRegExp: /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
    };
  },
  methods: {
    isNameValid: function() {
      if (this.name === "") {
        this.invalidName = true;
        return false;
      }
      this.invalidName = false;
      return true;
    },
    isPasswordValid: function() {
      if (
        this.password === "" ||
        this.confirmedPassword === "" ||
        this.password !== this.confirmedPassword
      ) {
        this.invalidPassword = true;
        return false;
      }
      this.invalidPassword = false;
      return true;
    },
    isEmailValid: function() {
      if (!this.emailRegExp.test(this.email)) {
        this.invalidEmail = true;
        return false;
      }
      this.invalidEmail = false;
      return true;
    },
    signUpFunction: async function() {
      if (this.isEmailValid() && this.isNameValid() && this.isPasswordValid()) {
        await signUp(this.name, this.email, this.password);
        await Router.push("/");
      } else {
        //TODO Faire une notif pour dire que ça fail
      }
    }
  }
};
</script>

<style scoped>
.main-container {
  max-width: 600px;
}
</style>
