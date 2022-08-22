<template>
    <div class="dashboard">
      <!-- Header -->
      <header>
        <h4 v-if="isPlaying">Now playing:</h4>
        <h4 v-else>Paused</h4>
        <h2>{{currentSongInfo.name}}</h2>
      </header>
  
      <!-- CD -->
      <div class="cd" ref="cd">
        <div class="cd-thumb" ref="cdThumb" :style="{ 'background-image': 'url(' + currentSongInfo.image + ')' }"></div>
      </div>
  
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
</template>

<script>
export default {
  name: 'MusicDasboard',
  props: ['currentSongInfo', 'songInfos', 'currentIndex'],
  data() {
    return {
      cdThumdAnimate: '',
      isPlaying: false,
      isRepeat: false,
      isRandom: false
    }
  },
  watch: {
    currentSongInfo (newValue) {
      this.currentSongInfo = newValue;
      this.$refs.audio.src = this.currentSongInfo.path.default;
      if (this.isPlaying) {
        this.$refs.audio.play();
      }
    },
  },
  methods: {
    //Handle music playback when pressing Play
    onPlay() {
      if (this.isPlaying) {
          this.$refs.audio.pause();
      } else {
          this.$refs.audio.play();
      };

      this.$refs.audio.onplay = () => {
          this.isPlaying = true;
          this.$emit('reactPlayer', this.isPlaying);
          this.cdThumdAnimate.play();
      };

      this.$refs.audio.onpause = () => {
          this.isPlaying = false;
          this.$emit('reactPlayer', this.isPlaying);
          this.cdThumdAnimate.pause();
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
        let _currentIndex = this.currentIndex;
        _currentIndex++;
        if (_currentIndex >= this.songInfos.length) {
            _currentIndex = 0;
        }
        this.$emit('updateCurrentIndex', _currentIndex);
      }
    },
    //Handle when prev song
    prevSong() {
      if (this.isRandom) {
          this.playRandomSong();
      } else {
        let _currentIndex = this.currentIndex;
        _currentIndex--;
        if (_currentIndex < 0) {
            _currentIndex = this.songInfos.length - 1;
        }
        this.$emit('updateCurrentIndex', _currentIndex);
      }
    },
    //Handle when random song
    randomSong() {
      this.isRandom = !this.isRandom;
      this.$refs.btnRandom.classList.toggle('active');
    },
    playRandomSong() {
      let newIndex;
      let _currentIndex = this.currentIndex;
      do {
          newIndex = Math.floor(Math.random() * this.songInfos.length);
      } while (newIndex === _currentIndex);
      _currentIndex = newIndex;
      this.$emit('updateCurrentIndex', _currentIndex);
    },
    //Handle when repeat song
    repeatSong() {
      this.isRepeat = !this.isRepeat;
      this.$refs.btnRepeat.classList.toggle('active');
    }
  },
  mounted() {
    // Handle Stop and Rotate of CD
    this.cdThumdAnimate = this.$refs.cdThumb.animate([
            { transform: 'rotate(360deg)' }
        ], {
            duration: 10000,
            iterations: Infinity
    });
    this.cdThumdAnimate.pause();

    // Handle zoom in and zoom out CD when scroll screen
    const cdWidth = this.$refs.cd.offsetWidth;
    document.onscroll = () => {
        let scrollTop = window.scrollY || window.scrollTop;
        if (scrollTop == undefined) {
          scrollTop = 0;
        }
        const newCdWidth = cdWidth - scrollTop;
        this.$refs.cd.style.width = newCdWidth > 0 ? newCdWidth + 'px' : 1;
        this.$refs.cd.style.opacity = newCdWidth / cdWidth;
    }
  }
}
</script>