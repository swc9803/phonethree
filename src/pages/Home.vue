<template>
  <transition name="loadingFade">
    <div v-show="phoneLoading" class="loading">Loading...</div>
  </transition>
  <div class="section1">
    <Phone @passCover="passCover" />
  </div>
  <div class="section2">
    <Accessories :cover="cover" />
  </div>
</template>

<script>
import { ref } from 'vue'
import Phone from '@/components/Phone.vue'
import Accessories from '@/components/Accessories.vue'
export default {
  setup () {
    const phoneLoading = ref(true)
    const cover = ref()
    const passCover = (coverData) => {
      cover.value = coverData
    }
    setTimeout(() => {
      phoneLoading.value = false
      document.querySelector('#app').style.overflow = 'visible'
    }, 1700)
    return {
      phoneLoading,
      cover,
      passCover
    }
  },
  components: {
    Phone,
    Accessories
  }
}
</script>

<style lang="scss" scoped>
.loading {
  position: absolute;
  width: 100%;
  height: 100vh;
  background: rgb(20, 20, 20);
  color: rgb(255, 255, 255);
  font-size: 5em;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  z-index: 10;
}
.loadingFade-enter-active,
.loadingFade-leave-active {
  transition: all 0.5s ease;
}
.loadingFade-enter-from,
.loadingFade-leave-to {
  opacity: 0;
  transform: translateY(-30px);
}
.section1 {
  width: 100%;
  height: 1000vh;
  background: rgb(20, 20, 20);
  p {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
    margin: 0;
    word-break: keep-all;
    text-align: center;
    color: white;
    position: relative;
    z-index: 1;
  }
}
.section2 {
  position: relative;
  width: 100%;
  height: 100vh;
  z-index: 3;
}
</style>
