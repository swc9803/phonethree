<template>
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
    const cover = ref()
    const passCover = (coverData) => {
      cover.value = coverData
    }
    return {
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
