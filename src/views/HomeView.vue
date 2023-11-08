<template>
  <div class="grid grid-cols-2 md:grid-cols-3 gap-4">
    <div
      v-for="thumbnail in thumbnails"
      :key="thumbnail[0].metadata.id"
      class="thumbnail-item"
    >
      <div>
        <img
          :src="thumbnail[0].file"
          @click="
            clickedVideo(
              thumbnail[0].metadata.id,
              thumbnail[0].metadata.user,
              thumbnail[0].metadata.title
            )
          "
          class="h-auto max-w-full rounded-lg cursor-pointer"
        />
        <p>
          {{ thumbnail[0].metadata.title }} - {{ thumbnail[0].metadata.user }}
        </p>
      </div>
    </div>
  </div>
</template>

<style>
.videos-container {
  display: flex;
  flex-wrap: wrap;
}

.thumbnail-item {
  margin-top: 20px;
  flex: 1;
  display: flex;
  justify-content: center;
}
.thumbnail-item img {
  height: 300px;
  width: 400px;
  cursor: pointer;
}
</style>

<script>
import axios from "axios";
import { useAuthStore } from "../stores/store";
export default {
  data() {
    return {
      user: "",
      thumbnails: [],
    };
  },
  methods: {
    clickedVideo(id, user, title) {
      axios
        .post("http://localhost:5000/api/set_videod", {
          id: id,
          title: title,
          i: 0,
        })
        .then((response) => {
          this.$router.push("/playback");
        })
        .catch((error) => {
          console.error(error);
          alert("It ain't good");
          this.$router.push("/home");
        });
    },
    sortVids() {
      let axiosRequests = [];
      for (let thumbnail of this.thumbnails) {
        axiosRequests.push(
          axios
            .get("http://localhost:5000/api/views/" + thumbnail[0].metadata.id)
            .then((response) => {
              thumbnail[0]["views"] = response.data.views;
            })
        );
      }
      Promise.all(axiosRequests)
        .then(() => {
          this.thumbnails.sort(function (a, b) {
            if (parseInt(a[0]["views"]) < parseInt(b[0]["views"])) return 1;
            if (parseInt(a[0]["views"]) > parseInt(b[0]["views"])) return -1;
            return 0;
          });
          console.log(this.thumbnails);
        })
        .catch((error) => {
          console.error("Error fetching views:", error);
        });
    },
  },
  beforeMount() {
    axios
      .get("http://localhost:5000/api/thumbnails")
      .then((response) => {
        this.thumbnails = response.data.thumbnails;
        this.sortVids();
      })
      .catch((error) => {
        console.error("Couldn't fetch thumbnails:", error);
      });
  },
  created() {
    axios
      .get("http://localhost:5000/api/fetch_username")
      .then((response) => {
        this.user = response.data.name;
        console.log("user: ", response.data.name);
        axios
          .post("http://localhost:5000/api/get_token", {
            username: response.data.name,
          })
          .then((response) => {
            const auth = useAuthStore();
            auth.setToken(response.data.token);
          })
          .catch((error) => {
            console.error(error);
            alert("Login again");
            this.$router.push("/");
          });
      })
      .catch((error) => {
        console.error(error);
        alert("Login again");
        this.$router.push("/");
      });
  },
};
</script>
