<template>
  <div
    class="max-w-screen-xl flex flex-wrap items-center justify-between mx-auto p-4 border-gray-200"
  >
    <RouterLink to="/" class="flex items-center">
      <img src="@/assets/toktik.png" class="h-8 mr-3" alt="toktik logo" />
      <span
        class="self-center text-2xl font-semibold whitespace-nowrap"
        ><RouterLink to="/home">
          <button class="text-white">TokTik</button>
        </RouterLink></span
      >
    </RouterLink>
    <div class="flex md:order-2">
      <button
        type="button"
        @click="logout"
        to="/"
        class="text-white bg-red-500 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-4 py-2 text-center mr-3 md:mr-0"
      >
        Logout
      </button>
    </div>
    <div
      class="items-center justify-between hidden w-full md:flex md:w-auto md:order-1"
    >
      <ul
        class="flex flex-col p-4 md:p-0 mt-4 font-medium rounded-lg md:flex-row md:space-x-8 md:mt-0"
      >
        <li>
          <RouterLink
            to="/list"
            class="blockt text-white rounded hover:bg-gray-100 md:hover:bg-transparent md:hover:text-red-500 md:p-0 md:dark:hover:text-red-500"
            >My Videos</RouterLink
          >
        </li>
        <li>
          <RouterLink
            to="/upload"
            class="block text-white rounded hover:bg-gray-100 md:hover:bg-transparent md:hover:text-red-500 md:p-0 md:dark:hover:text-red-500"
          >
            <button>Upload</button>
          </RouterLink>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { useAuthStore } from "../stores/store";
export default {
  methods: {
    logout() {
      axios
        .post("http://localhost:5000/api/logout")
        .then((response) => {
          const auth = useAuthStore();
          auth.clearToken;
          this.$router.push('/');
        })
        .catch((error) => {
          console.error(error);
        });
    },
  },
};
</script>
