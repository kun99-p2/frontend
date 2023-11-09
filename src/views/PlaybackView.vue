<template>
  <div class="flex justify-center">
    <div class="flex flex-col items-start">
      <div class="flex items-center">
        <div class="text-white cursor-pointer" @click="navigate('left')">
          &#8249;
        </div>
        <div class="mx-4">
          <video
            class="video-js"
            ref="videoPlayer"
            @ended="navigate('right')"
          ></video>
        </div>
        <div class="text-white cursor-pointer" @click="navigate('right')">
          &#8250;
        </div>
      </div>
      <div class="flex flex-col justify-start ml-5 w-full">
        <div class="flex flex-row">
          <div class="w-5/6">
            <p class="text-white font-bold text-2xl">{{ this.title }}</p>
          </div>
          <div class="w-1/6">
            <p class="text-white">Views: {{ this.views }}</p>
          </div>
        </div>
        <div>
          <p class="text-white">{{ this.desc }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import videojs from "video.js";
export default {
  data() {
    return {
      id: "",
      user: "",
      video: "",
      videos: [],
      title: "",
      desc: "",
      views: "",
      i: 0,
      m3u8: "",
      player: null,
    };
  },
  methods: {
    clickedVideo(id, title) {
      axios
        .post("/api/set_videod", {
          id: id,
          title: title,
          i: this.i,
        })
        .catch((error) => {
          console.error(error);
          alert("It ain't good");
          this.$router.push("/home");
        });
      this.$router.go();
    },
    navigate(direction) {
      if (direction === "right") {
        if (this.i >= this.videos.length - 1) {
          this.i = 0;
        } else {
          this.i++;
        }
      } else {
        this.i = this.i > 0 ? this.i - 1 : this.videos.length - 1;
      }
      this.clickedVideo(
        this.videos[this.i][0].metadata.id,
        this.videos[this.i][0].metadata.title
      );
    },
    async increaseViews(videoId) {
      try {
        const response = await axios.post("/api/increment/" + videoId);
        this.views = response.data.views;
      } catch (error) {
        console.error("Couldn't increment views.");
      }
    },
    initVideo() {
      this.player = videojs(this.$refs.videoPlayer, {
        controls: true,
        autoplay: true,
        sources: [
          {
            src: this.m3u8,
            type: "application/x-mpegURL",
          },
        ],
      });
    },
  },
  mounted() {
    axios
      .get("/api/videod")
      .then((response) => {
        this.user = response.data.user;
        this.id = response.data.id;
        this.title = response.data.title;
        this.i = response.data.i;
        console.log(this.i);
        //m3u8
        axios
          .post("/api/hls", {
            user: this.user,
            title: this.title,
          })
          .then((response) => {
            console.log(response.data);
            this.title = response.data.metadata.title;
            this.desc = response.data.metadata.desc;
            this.m3u8 = response.data.m3u8;
            this.increaseViews(this.id);
            this.initVideo();
            this.player.on("pause", () => {
              this.$refs.videoPlayer.pause();
            });
            this.player.on("play", () => {
              this.$refs.videoPlayer.play();
            });
          })
          .catch((error) => {
            console.error("Couldn't fetch video:", error);
          });
      })
      .catch((error) => {
        console.error("Couldn't fetch videos:", error);
      });
    axios
      .get("/api/videos")
      .then((response) => {
        this.videos = response.data.videos;
        console.log(this.videos);
      })
      .catch((error) => {
        console.error("Couldn't fetch videos:", error);
      });
  },
  beforeDestroy() {
    if (this.player) {
      this.player.dispose();
    }
  },
};
</script>
