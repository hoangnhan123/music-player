<template>
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
</template>

<script>
export default {
    name: 'MusicPlaylist',
    props: ['songInfos', 'currentIndex'],
    data() {
        return {
        }
    },
    watch: {
        currentIndex (newCurrentIndex) {
            for (let index = 0; index < this.$refs.song.length; index++) {
                this.$refs.song[index].classList.remove('active');
            }
            this.$refs.song[newCurrentIndex].classList.add('active');
        },
    },
    methods: {
      playCurrentSong(e) {
        this.$emit('playCurrentSong', e);
      }
    },
    mounted() {
        this.$refs.song[this.currentIndex].classList.add('active');
    }
}
</script>