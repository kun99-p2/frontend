<template>
  <div class="id">
    <h1 class="text-white">Registration</h1>
    <input v-model="username" placeholder="Username">
    <input v-model="password" placeholder="Password" type="password">
  </div>
  <div class="submit">
    <button class="bg-white" @click="cancel" style="margin-right: 10px;">Cancel</button>
    <button class="bg-white" @click="register">Register</button>
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
import axios from 'axios';
export default {
  data() {
    return {
      username: '',
      password: ''
    };
  },
  methods: {
    register() {
      axios.post('http://localhost:5000/api/register', {
        username: this.username,
        password: this.password
      })
      .then(response => {
        console.log(response.data);
        this.$router.push('/success');
      })
      .catch(error => {
        console.error(error);
      });
    },
    cancel() {
      this.$router.push('/');
    }
  }
};
</script>
  