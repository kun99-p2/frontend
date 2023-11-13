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
        <div class="flex flex-row">
          <div class="w-5/6">
            <p class="text-white">{{ this.desc }}</p>
          </div>
          <div class="w-1/6">
            <button @click="increaseLikes" class="text-white bg-primary">
              Likes
              {{ this.likes }}
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
  <Comments/>
</template>

<script>
import { useAuthStore } from "../stores/store.js";
import axios from "axios";
import videojs from "video.js";
import io from "socket.io-client";
import Comments from "../components/Comments.vue";
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
            likes: "",
            i: 0,
            m3u8: "",
            player: null,
            socket: null,
        };
    },
    methods: {
        setupSocket() {
            this.socket = io.connect("http://localhost:5000");
            this.socket.on("update_views", (views) => {
                console.log(views);
                this.views = views.views;
            });
            this.socket.on("update_likes", (likes) => {
                this.likes = likes.likes;
            });
        },
        // notifyWebSocketServer(views) {
        //   this.socket.emit("update_views", views);
        // },
        // notifyWebSocketServerLikes() {
        //   this.socket.emit("update_likes", this.likes);
        // },
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
                }
                else {
                    this.i++;
                }
            }
            else {
                this.i = this.i > 0 ? this.i - 1 : this.videos.length - 1;
            }
            this.clickedVideo(this.videos[this.i][0].metadata.id, this.videos[this.i][0].metadata.title);
        },
        increaseViews() {
            try {
                axios.post("/api/increment/" + this.id).then((response) => {
                    this.views = response.data.views;
                    //this.notifyWebSocketServer(this.views);
                });
            }
            catch (error) {
                console.error("Couldn't increment views.");
            }
        },
        increaseLikes() {
            try {
                axios.post("/api/like/" + this.id).then((response) => {
                    this.likes = response.data.likes;
                    //this.notifyWebSocketServerLikes();
                });
            }
            catch (error) {
                console.error("Couldn't increment likes.");
            }
        },
        async getLikes() {
            const response = await axios.get("/api/views/" + this.id);
            this.likes = response.data.likes;
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
            //m3u8
            axios
                .post("/api/hls", {
                user: this.user,
                title: this.title,
            })
                .then((response) => {
                console.log(response.data);
                this.desc = response.data.metadata.desc;
                this.m3u8 = response.data.m3u8;
                this.setupSocket();
                this.increaseViews();
                this.getLikes();
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
    components: { Comments }
};
</script>
