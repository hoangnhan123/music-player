<template>
    <div class="cd" ref="cd">
        <div class="cd-thumb" ref="cdThumb" :style="{ 'background-image': 'url(' + image + ')' }"></div>
    </div>
</template>

<script>

export default {
  name: 'CDComponent',
  props: {
    image: String,
    isPlaying: Boolean
  },
  data() {
    return {
        cdThumdAnimate: ''
    }
  },
  watch: {
    isPlaying (newValue) {
      if (newValue) {
        this.cdThumdAnimate.play();
      } else {
        this.cdThumdAnimate.pause();
      }
    },
  },
  methods: {
  },
  computed: {
  },
  mounted() {
    // Xử lý CD/ Quay dừng
    this.cdThumdAnimate = this.$refs.cdThumb.animate([
            { transform: 'rotate(360deg)' }
        ], {
            duration: 10000,
            iterations: Infinity
    });
    this.cdThumdAnimate.pause();

    // Xử lý phóng to // thu nhỏ
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

<style scoped>
/* CD */
.cd {
    display: flex;
    margin: auto;
    width: 200px;
}

.cd-thumb {
    width: 100%;
    padding-top: 100%;
    border-radius: 50%;
    background-color: #333;
    background-size: cover;
    margin: auto;
}
</style>
