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
      <div class="player">
        <!-- Dashboard -->
        <div class="dashboard">
          <!-- Header -->
          <header>
            <h4>Now playing:</h4>
            <h2 ref="songName">String 57th & 9th</h2>
          </header>
      
          <!-- CD -->
          <div class="cd">
            <div class="cd-thumb" ref="cdThumb" style="background-image: url('https://i.ytimg.com/vi/jTLhQf5KJSc/maxresdefault.jpg')">
            </div>
          </div>
      
          <!-- Control -->
          <div class="control">
            <div class="btn btn-repeat">
              <i class="fas fa-redo"></i>
            </div>
            <div class="btn btn-prev">
              <i class="fas fa-step-backward"></i>
            </div>
            <div class="btn btn-toggle-play">
              <i class="fas fa-pause icon-pause"></i>
              <i class="fas fa-play icon-play"></i>
            </div>
            <div class="btn btn-next">
              <i class="fas fa-step-forward"></i>
            </div>
            <div class="btn btn-random">
              <i class="fas fa-random"></i>
            </div>
          </div>
      
          <input id="progress" class="progress" type="range" value="0" step="1" min="0" max="100">
      
          <audio id="audio" src="" ref="audio"></audio>
        </div>
      
        <!-- Playlist -->
        <div class="playlist" @click="playCurrentSong">
          <div v-for="(song, index) in songInfos">
            <div class="song" :data-index="index">
                <img class="thumb" :src="require('@/assets/img/' + song.image)"/>
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

export default {
  name: 'MusicApp',
  data() {
    return {
      songInfos: songInfos,
      currentIndex: 0,
      currentSongInfo : '',
      status: 'Now Playing',
      songName: 'String 57th & 9th'
    }
  },
  methods: {
    playCurrentSong (e) {
      const songNode = e.target.closest('.song:not(.active)');
      const songOption = e.target.closest('.option');
      if (songNode || songOption) {
          if (songNode) {
              this.currentIndex = Number(songNode.dataset.index);
              this.loadcurrentSongInfo();
              this.$refs.audio.src = this.currentSongInfo.path.default;
              this.$refs.audio.play();
          }
          if (songOption) {
              //.........
          }
          
      } else {
          // do nothing
      }
    },
    loadcurrentSongInfo () {
      this.currentSongInfo = this.songInfos[this.currentIndex];
      this.$refs.songName.textContent = this.currentSongInfo.name;
      this.$refs.cdThumb.style.backgroundImage = 'url(' + require('@/assets/img/' + this.currentSongInfo.image) + ')';
    }
  },
  computed: {
  },
  mounted() {
    this.loadcurrentSongInfo();
  }
}
</script>

<style>
    @import '@/assets/css/styles.css';
</style>
