<template>
  <div class="w-screen min-h-screen container mx-auto py-10">
    <!-- nav -->
    <div class="flex justify-between">
      <button
        @click="exitRoom"
        v-show="room.canJoin"
        class="py-4 px-5 bg-red-500 rounded-full flex justify-center items-center"
      >
        <i class="fas fa-chevron-left fa-2x text-white"></i>
      </button>
      <div
        class="py-4 px-5 bg-blue-500 text-white font-bold rounded-full text-xl flex justify-center items-center"
      >
        {{ room.players.length }} <i class="fas fa-user ml-3"></i>
      </div>
      <div
        v-show="!room.canJoin"
        class="py-4 px-5 bg-yellow-500 text-white font-bold rounded-full text-xl flex justify-center items-center"
      >
        Thời gian : {{ Math.max(room.questionTimeLeft, 0) }}s
      </div>

      <div
        v-show="!room.canJoin"
        class="py-4 px-5 bg-green-500 text-white font-bold rounded-full text-xl flex justify-center items-center"
      >
        Số điểm: {{ myScore }}
      </div>
    </div>
    <div class="w-full h-full">
      <PlayerList v-show="room.canJoin" />
      <transition v-if="!room.canJoin" tag="div" name="fadeslide" mode="out-in">
        <component v-bind:is="room.view"></component>
      </transition>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import PlayerList from "./PlayerList";
import Question from "./Question";
import Leaderboard from "./leaderboard";
export default {
  name: "MultiPlay",
  components: {
    PlayerList,
    question: Question,
    leaderboard: Leaderboard,
  },
  data() {
    return {};
  },
  mounted() {
    if (this.room == false) this.$router.push("/");
  },
  computed: {
    room() {
      return this.$store.state.room;
    },
    myScore() {
      let you;
      for (let p of this.room.players) if (p.id == this.$socket.id) you = p;
      return you.score;
    },
  },
  methods: {
    exitRoom() {
      this.$socket.emit("leaveRoom");
      setTimeout(() => {
        this.$store.dispatch("exitRoom");
        this.$router.back();
      }, 500);
    },
  },
};
</script>
<style>
.fadeslide-enter-active {
  transition: all 0.5s ease-in-out;
}
.fadeslide-enter {
  opacity: 0;
  transform: translateY(60px);
}
.fadeslide-leave-active {
  transition: all 0.5s ease-in-out;
  opacity: 0;
  transform: translateY(60px);
}
</style>
