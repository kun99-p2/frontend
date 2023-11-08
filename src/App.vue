<script setup>
import NavBar from "./components/NavBar.vue";
import { RouterLink, RouterView } from "vue-router";
</script>

<template>
  <div>
    <nav class="z-20 top-0 left-0 border-b  border-gray-800 ">
      <NavBar />
    </nav>
    <RouterView/>
  </div>
</template>

<style></style>
