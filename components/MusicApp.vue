<template>
  <div class="player" ref="player">
    <!-- Dashboard -->
    <MusicDasboard 
      :songInfos="songInfos" 
      :currentSongInfo="currentSongInfo"
      :currentIndex="currentIndex"
      @reactPlayer="changeClassPlayer($event)" 
      @updateCurrentIndex="updateCurrentIndex($event)"/>
  
    <!-- Playlist -->
    <MusicPlaylist 
      :songInfos="songInfos" 
      @playCurrentSong="playCurrentSong($event)"
      :currentIndex="currentIndex"
      />
  </div>
</template>

<script>
import songInfos from '@/assets/music/list-song.js';
import MusicDasboard from '@/components/MusicDasboard.vue';
import MusicPlaylist from '@/components/MusicPlaylist.vue';

export default {
  name: 'MusicApp',
  components: {
    MusicDasboard,
    MusicPlaylist
  },
  data() {
    return {
      songInfos: songInfos,
      currentIndex: 0,
      currentSongInfo : ''
    }
  },
  methods: {
    //Handle save current song information
    loadCurrentSongInfo () {
        this.currentSongInfo = this.songInfos[this.currentIndex];
    },
    //Handle music playback when click
    playCurrentSong (e) {
        const currentSongNode = e.target.closest('.song:not(.active)');
        const songOption = e.target.closest('.option');
        if (currentSongNode || songOption) {
            if (currentSongNode) {
                this.currentIndex = Number(currentSongNode.dataset.index);
                this.loadCurrentSongInfo();
            }
            if (songOption) {
                //.........
            }
        } else {
            // do nothing
        }
    },
    // Handle change class list player
    changeClassPlayer(isPlaying) {
      if (isPlaying) {
        this.$refs.player.classList.add('playing');
      } else {
        this.$refs.player.classList.remove('playing');
      }
    },
    // Handle change class list player
    updateCurrentIndex(newCurrentIndex) {
      this.currentIndex = newCurrentIndex;
      this.loadCurrentSongInfo();
    }
  },
  mounted() {
    this.loadCurrentSongInfo();
  }
}
</script>
