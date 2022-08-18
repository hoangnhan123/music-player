<template>
    <html lang="en">
    <head>
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Music player</title>
      <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.2/css/all.min.css" integrity="sha512-HK5fgLBL+xu6dm/Ii3z4xhlSUyZgTT9tuc/hSrtw6uzJOvgRr2a9jyxxT1ely+B+xFAmJKVSTbpM/CuL7qxO8w==" crossorigin="anonymous" />
      <link rel="preconnect" href="https://fonts.gstatic.com">
      <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600&display=swap" rel="stylesheet">
    </head>
    <body>
      <div class="player" ref="player">
        <!-- Dashboard -->
        <div class="dashboard">
          
          <!-- Header -->
          <header>
            <h4>Now playing:</h4>
            <h2 ref="songName"></h2>
          </header>
      
          <!-- CD -->
          <CD :image="currentSongInfo.image" :isPlaying="isPlaying"/>
      
          <!-- Control -->
          <div class="control">
            <div class="btn btn-repeat" ref="btnRepeat" @click="repeatSong">
              <i class="fas fa-redo"></i>
            </div>
            <div class="btn btn-prev" @click="prevSong">
              <i class="fas fa-step-backward"></i>
            </div>
            <div class="btn btn-toggle-play" @click="onPlay">
              <i class="fas fa-pause icon-pause"></i>
              <i class="fas fa-play icon-play"></i>
            </div>
            <div class="btn btn-next" ref="btnNext" @click="nextSong">
              <i class="fas fa-step-forward"></i>
            </div>
            <div class="btn btn-random" ref="btnRandom" @click="randomSong">
              <i class="fas fa-random"></i>
            </div>
          </div>
      
          <input id="progress" ref="progress" class="progress" type="range" value="0" step="1" min="0" max="100">
      
          <audio id="audio" src="" ref="audio"></audio>
        </div>
      
        <!-- Playlist -->
        <div class="playlist">
          <div v-for="(song, index) in songInfos">
            <div class="song" ref="song" :data-index="index" @click="playCurrentSong">
                <img class="thumb" :src="song.image"/>
                <div class="body">
                    <h3 class="title">{{song.name}}</h3>
                    <p class="author">{{song.singer}}</p>
                </div>
                <div class="option">
                    <i class="fas fa-ellipsis-h"></i>
                </div>
            </div>
          </div>
        </div>
      </div>
    </body>
    </html>
</template>

<script>
import songInfos from '@/assets/music/list-song.js';
import CD from '@/components/CD.vue';

export default {
  name: 'MusicApp',
  components: {
    CD
  },
  data() {
    return {
      songInfos: songInfos,
      currentIndex: 0,
      currentSongInfo : '',
      currentSongNode: '',
      isPlaying: false,
      isRepeat: false,
      isRandom: false,
      status: 'Now Playing',
      songName: 'String 57th & 9th'
    }
  },
  methods: {
    //Handle music playback when click
    playCurrentSong (e) {
      this.currentSongNode = e.target.closest('.song:not(.active)');
      const songOption = e.target.closest('.option');
      if (this.currentSongNode || songOption) {
          if (this.currentSongNode) {
              this.currentIndex = Number(this.currentSongNode.dataset.index);
              this.loadcurrentSongInfo();
              this.$refs.audio.src = this.currentSongInfo.path.default;
              if (this.isPlaying) {
                this.$refs.audio.play();
              }
          }
          if (songOption) {
              //.........
          }
      } else {
          // do nothing
      }
    },
    //Handle music playback when pressing Play
    onPlay() {
      if (this.isPlaying) {
          this.$refs.audio.pause();
      } else {
          this.$refs.audio.play();
      };

      this.$refs.audio.onplay = () => {
          this.isPlaying = true;
          this.$refs.player.classList.add('playing');
      };

      this.$refs.audio.onpause = () => {
          this.isPlaying = false;
          this.$refs.player.classList.remove('playing');
      };

      this.$refs.audio.ontimeupdate = () => {
          if (this.$refs.audio.duration) {
              const progressPercent = Math.floor(this.$refs.audio.currentTime / this.$refs.audio.duration*100);
              this.$refs.progress.value = progressPercent;
          }
      };

      //Handle progress
      this.$refs.progress.onchange = (e) => {
          const seekTime = this.$refs.audio.duration / 100 * e.target.value;
          this.$refs.audio.currentTime = seekTime;
      };

      //Handle at the end of the song
      this.$refs.audio.onended = () => {
          if (this.isRepeat) {
              this.$refs.audio.play();
          } else {
              if (!(this.currentIndex == this.songInfos.length - 1)) {
                  this.isPlaying = true;
              }
              this.$refs.progress.value = 0;
              this.$refs.btnNext.click();
          }
      }
    },
    //Handle when next song
    nextSong() {
      if (this.isRandom) {
        this.playRandomSong();
      } else {
        this.currentIndex++;
        if (this.currentIndex >= this.songInfos.length) {
            this.currentIndex = 0;
        }
        this.loadcurrentSongInfo();
      }
      if (this.isPlaying) {
        this.$refs.audio.play();
      }
    },
    //Handle when prev song
    prevSong() {
      if (this.isRandom) {
          this.playRandomSong();
      } else {
        this.currentIndex--;
        if (this.currentIndex < 0) {
            this.currentIndex = this.songInfos.length - 1;
        }
        this.loadcurrentSongInfo();
      }
      if (this.isPlaying) {
        this.$refs.audio.play();
      }
    },
    //Handle when random song
    randomSong() {
      this.isRandom = !this.isRandom;
      this.$refs.btnRandom.classList.toggle('active');
    },
    playRandomSong() {
      let newIndex;
      do {
          newIndex = Math.floor(Math.random() * this.songInfos.length);
      } while (newIndex === this.currentIndex);
      this.currentIndex = newIndex;
      this.loadcurrentSongInfo();
    },
    //Handle when repeat song
    repeatSong() {
      this.isRepeat = !this.isRepeat;
      this.$refs.btnRepeat.classList.toggle('active');
    },

    //Handle save current song information
    loadcurrentSongInfo () {
      this.currentSongInfo = this.songInfos[this.currentIndex];
      this.$refs.songName.textContent = this.currentSongInfo.name;
      this.$refs.audio.src = this.currentSongInfo.path.default;

      for (let index = 0; index < this.$refs.song.length; index++) {
        this.$refs.song[index].classList.remove('active');
      }
      this.$refs.song[this.currentIndex].classList.add('active');
    },
  },
  computed: {
  },
  //Load top song when load first time.
  mounted() {
    this.loadcurrentSongInfo();
  }
}
</script>

<style>
    @import '@/assets/css/styles.css';
</style>
