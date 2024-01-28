<template>
  <div class="music">
      
      <div class="video">
          <YouTube 
              v-if="currentVideo"
              :src="currentVideo.id"
              @ready="ready"
              @state-change="stateChange"
              :vars="{start: 0, autoplay: 0}"
              class="video-player"
          />
      </div>

      <div class="result d-flex flex-column justify-center align-center pa-2">
          <div>
            <div class="d-flex justify-center">
                <span class="corrects">{{ corrects }}</span>&nbsp;&nbsp;
                <span class="wrongs">{{ wrongs }}</span>
            </div>
            <div>{{ currentVideo && currentVideo.title }}</div>
          </div>

          <v-btn variant="tonal" @click="shuffle()" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-full">Shuffle</v-btn>
      </div>
      
      <div class="options d-flex flex-wrap">
          <v-btn variant="tonal" v-for="(choice, index) in choices" :key="index" @click="guess(choice)" class="flex-grow-1 ma-2">
              {{ choice }}
          </v-btn>
      </div>

      <v-snackbar
        v-model="showResult"
        :color="lastGuessCorrect ? 'green-darken-2' : 'red-lighten-2'"
        :timeout="1000"
        absolute
        bottom
      >
        {{ lastGuessCorrect ? 'RÄTT' : 'FEL' }}
      </v-snackbar>
  </div>
  <Visualizer :playing="playerState === playerStates.playing"/>
</template>

<script setup>
  import { ref, computed, watch } from 'vue';
  import YouTube from 'vue3-youtube'
  import Visualizer from './Visualizer.vue';

  let corrects = ref(0);
  let wrongs = ref(0);
  let currentVideo = ref(null);
  let currentChoice = ref(null);
  let player = ref(null);
  const showResult = ref(false);
  const lastGuessCorrect = ref(false);

  const playerStates = {
    unstarted: -1,
    ended: 0,
    playing: 1,
    paused: 2,
    buffering: 3,
    cued: 5,
  }
  const playerState = ref(playerStates.unstarted)

  let videos = [
    {title: 'Konstmusik orkester', id: '1lHOYvIhLxo'},
    {title: 'Opera', id: 'hAoe7QdZ3ao'},
    {title: 'Körmusik', id: 'eHl8r2g4MXI'},
    {title: 'Folkmusik', id: 'wVx5RJojwnA'},
    {title: 'Kulning', id: 'L7sZ4kx7kQc'},
    {title: 'Irländsk folkmusik', id: 'L5JyVFfBsIg'},
    {title: 'Irländsk folkmusik', id: 'h_IlrRJRXcM'},
    {title: 'Kulning', id: 'KKImmIQ4omQ'},
    {title: 'Blues', id: 'MpRIYi721WE'},
    {title: 'Jazz', id: 'kmfeKUNDDYs'},
    {title: 'Jazz', id: 'qkthxBsIeGQ'},
    {title: 'Rock', id: 'gj0Rz-uP4Mk'},
    {title: 'Rock', id: 'F5fsqYctXgM'},
    {title: 'Pop', id: 'm2uTFF_3MaA'},
    {title: 'Pop', id: 'NEjkftp7J7I'},
    {title: 'Pop', id: 'poXvMBhjSWk'},
    {title: 'Pop', id: 'Sj_9CiNkkn4'},
    {title: 'Gospel', id: '1yUK0S_cEXY'},
    {title: 'Soul', id: 'Q8Tiz6INF7I'},
    {title: 'Country', id: 'JnX2BoZE9w4'},
    {title: 'Country', id: '97svDuqFctI'},
    {title: 'Hårdrock', id: 'eu5lv2Umn3M'},
    {title: 'Punk', id: 'a3UC7dWBDvg'},
    {title: 'Reggae', id: 'S5FCdx7Dn0o'},
    {title: 'Reggae', id: 'vdB-8eLEW8g'},
    {title: 'HipHop', id: 'u31FO_4d9TY'},
    {title: 'Dansbandsmusik', id: 'acf2mw6r8To'},
    {title: 'Dansbandsmusik', id: 'IGz61jY9zIU'},
    {title: 'Schlager', id: 'bSlxoRzrBMs'},
    {title: 'House', id: '1y6smkh6c-0'}
  ];

  let shuffle = () => {
    const randomIndex = Math.floor(Math.random() * videos.length);
    currentChoice.value = null;
    currentVideo.value = videos[randomIndex];
  };

  let ready = (event) => {
    player.value = event.target;
    console.log('ready');
    player.value.playVideo();
  };

  const stateChange = (event) => {
    console.log('stateChange', event);
    playerState.value = event.data;
  };

  let guess = (choice) => {
    if(!choice) return;

    currentChoice.value = choice;
    if (lastGuessCorrect.value) {
        corrects.value++;
        shuffle();
    } else {
        wrongs.value++;
    }
    showResult.value = true;
  };

  watch(
    () => currentChoice.value,
    (choice) => {
      lastGuessCorrect.value = choice && choice === currentVideo.value?.title;
    }
  )

  let choices = computed(() => {
    return videos.map(video => video.title).filter((v, i, a) => a.indexOf(v) === i);
  });

  shuffle()
</script>

<style scoped>
.video-player {
  width: 0 !important;
  height: 0 !important;
  visibility: hidden;
}

.result {
  position: sticky;
  top: 0;
}
</style>
```