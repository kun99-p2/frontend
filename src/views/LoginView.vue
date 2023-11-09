<template>
  <div class="id">
    <h1 class="text-white">Login</h1>
    <input v-model="username" placeholder="Username" />
    <input v-model="password" placeholder="Password" type="password" />
  </div>
  <div class="submit">
    <button class="bg-white" @click="cancel" style="margin-right: 10px">Cancel</button>
    <button class="bg-white" @click="login">Login</button>
  </div>
</template>

<style>
.id {
  font-size: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
  height: 20vh;
}
.submit {
  font-size: 20px;
  display: flex;
  flex-direction: row;
  justify-content: center;
  margin-left: 25px;
}
</style>

<script>
import axios from "axios";
import { useAuthStore } from "../stores/store.js";
export default {
  data() {
    return {
      username: "",
      password: "",
    };
  },
  methods: {
    login() {
      axios
        .post("/api/login", {
          username: this.username,
          password: this.password,
        })
        .then((response) => {
          const auth = useAuthStore();
          auth.setToken(response.data.token);
          //console.log(response.data.token);
          this.$router.push('/home');
        })
        .catch((error) => {
          console.error(error);
        });
    },
    cancel() {
      this.$router.push("/");
    },
  },
};
</script>
