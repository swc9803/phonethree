<template>
  <div class="section">
    <Phone />
  </div>
</template>

<script>
import Phone from '@/components/Phone.vue'
export default {
  setup () {

  },
  components: {
    Phone
  }
}
</script>

<style lang="scss" scoped>
.section {
  width: 100%;
  height: 400vh;
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
</style>
