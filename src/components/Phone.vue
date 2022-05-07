<template>
  <div class="cover" ref="cover" />
  <div class="renderPhone" ref="renderPhone" />
  <div class="intro">
    <p ref="intro1">놀라운 기능</p>
    <p ref="intro2">깔끔한 디자인</p>
    <p ref="intro3">모든 것을 갖춘</p>
  </div>
  <div class="description">
    <DrawLine class="draw" ref="draw" />
    <p v-for="description in descriptions" :key="description.id" :ref="stagger">
      {{ description.text }}
    </p>
  </div>
  <div class="maskrap">
    <div class="mask" ref="mask" />
  </div>
</template>

<script>
import { onMounted, ref } from 'vue'
import * as THREE from 'three'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'
import { DRACOLoader } from 'three/examples/jsm/loaders/DRACOLoader.js'
import gsap from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'
import DrawLine from '@/components/DrawLine.vue'
gsap.registerPlugin(ScrollTrigger)

export default {
  setup () {
    const cover = ref()
    const renderPhone = ref()
    const animation1 = gsap.timeline({ paused: true })
    const intro1 = ref()
    const intro2 = ref()
    const intro3 = ref()
    const draw = ref()
    const mask = ref()

    const descriptionArray = ref([])
    const stagger = (el) => descriptionArray.value.push(el)
    const description = ref()
    const descriptions = ref([
      { id: 1, text: '고사양 카메라' },
      { id: 2, text: '스피커' },
      { id: 3, text: '아이패드 / 아이팟' }])

    onMounted(() => {
      scrollTo(0, 0)
      function boxAni () {
        const fov = 75
        const aspect = renderPhone.value.clientWidth / renderPhone.value.clientHeight
        const near = 0.1
        const far = 2000
        const camera = new THREE.PerspectiveCamera(fov, aspect, near, far)
        camera.position.set(0, 0, 10)

        const renderer = new THREE.WebGLRenderer({
          antialias: true,
          alpha: true
        })
        const scene = new THREE.Scene()

        function init () {
          const color = 0xFFFFFF
          const intensity = 1
          const light = new THREE.DirectionalLight(color, intensity)
          light.position.set(-1, 2, 4)
          scene.add(light)
          renderer.setSize(renderPhone.value.clientWidth, renderPhone.value.clientHeight)
          renderer.setPixelRatio(window.devicePixelRatio)

          renderPhone.value.appendChild(renderer.domElement)

          // eslint-disable-next-line no-unused-vars
          function render () {
            renderer.render(scene, camera)
          }

          const gltfLoader = new GLTFLoader()

          const dracoLoader = new DRACOLoader()
          dracoLoader.setDecoderPath('https://raw.githubusercontent.com/mrdoob/three.js/dev/examples/js/libs/draco/')
          gltfLoader.setDRACOLoader(dracoLoader)
          const group = new THREE.Group()
          // iphone
          gltfLoader.load('/models/phone/phone.gltf', (model) => {
            const phone = model.scene
            phone.position.set(-0.1, -1.35, 7.5)
            phone.rotation.set(0, 0.2, 0)
            scene.add(phone)
            phone.format = THREE.RGBAFormat
            animation1.to(phone.rotation, {
              y: 360,
              duration: 360,
              repeat: -1,
              ease: 'none'
            })
            // ipad
            gltfLoader.load('/models/ipad/ipad.gltf', (model) => {
              const ipad = model.scene
              ipad.position.set(-0.1, -1.35, 7.5)
              ipad.rotation.set(0, 0.2, 0)
              group.add(ipad)
              ipad.format = THREE.RGBAFormat
            })
            // airpods
            gltfLoader.load('/models/airpods/airpods.gltf', (model) => {
              const airpods = model.scene
              airpods.position.set(-0.1, -1.35, -35)
              airpods.rotation.set(0, 0.2, 0)
              group.add(airpods)
              airpods.format = THREE.RGBAFormat
            })
            // macbook
            gltfLoader.load('/models/macbook/macbook.gltf', (model) => {
              const macbook = model.scene
              macbook.position.set(-0.5, -0.1, 9)
              macbook.rotation.set(0.5, 1.5, 0)
              group.add(macbook)
              macbook.format = THREE.RGBAFormat
            })
            // watch
            gltfLoader.load('/models/watch/watch.gltf', (model) => {
              const watch = model.scene
              watch.position.set(-5.5, -0.1, -180)
              watch.rotation.set(1.5, 0, 0)
              group.add(watch)
              scene.add(group)
              watch.format = THREE.RGBAFormat
            })

            // 글자 애니메이션
            const textScrollAni = gsap.timeline({
              scrollTrigger: {
                trigger: cover.value,
                start: '3% top',
                end: '25%',
                scrub: 1
              }
            })
            textScrollAni.fromTo(intro1.value, { xPercent: -120, opacity: 0 }, { xPercent: 0, opacity: 1 })
            textScrollAni.to(intro1.value, { xPercent: 120, opacity: 0 })
            textScrollAni.fromTo(intro2.value, { xPercent: -120, opacity: 0 }, { xPercent: 0, opacity: 1 })
            textScrollAni.to(intro2.value, { xPercent: 120, opacity: 0 })
            textScrollAni.fromTo(intro3.value, { xPercent: -120, opacity: 0 }, { xPercent: 0, opacity: 1 })
            textScrollAni.to(intro3.value, { xPercent: 120, opacity: 0 })

            // 폰 애니메이션
            const phoneScrollAni = gsap.timeline({
              scrollTrigger: {
                trigger: cover.value,
                start: '25% top', // 글자 애니메이션 end 후 바로
                end: '55%',
                scrub: 2,
                onLeaveBack: () => {
                  animation1.pause()
                  gsap.set(phone.rotation, { y: 0 })
                }
              }
            })
            phoneScrollAni
              .to(cover.value, { background: 'rgba(20, 20, 20, 0)' })
              .to(phone.rotation, { y: 6.3 }, '>')
              .to(phone.position, {
                x: 3,
                z: 3,
                onComplete () {
                  animation1.restart()
                  document.querySelector('.line').style.animationPlayState = 'running'
                }
              }, '<')
              .from('.line', { opacity: 0 }, '>')
              .fromTo(descriptionArray.value, { opacity: 0, xPercent: 10 }, { opacity: 1, xPercent: 0, stagger: 0.4 }, '>')

            // 마스크 애니메이션
            const maskScrollAni = gsap.timeline({
              scrollTrigger: {
                trigger: cover.value,
                start: '70% top',
                end: '100%',
                scrub: 2
              }
            })
            maskScrollAni
              .to(mask.value, { clipPath: 'circle(50%)' })
          })
          animate()
        }

        function animate () {
          requestAnimationFrame(animate)
          renderer.render(scene, camera)
        }
        init()

        function onWindowResize () {
          camera.aspect = renderPhone.value.clientWidth / renderPhone.value.clientHeight
          camera.updateProjectionMatrix()
          renderer.setSize(renderPhone.value.clientWidth, renderPhone.value.clientHeight)
        }
        window.addEventListener('resize', onWindowResize)
      }
      boxAni()
    })
    return {
      cover,
      renderPhone,
      intro1,
      intro2,
      intro3,
      draw,
      descriptionArray,
      stagger,
      description,
      descriptions,
      mask
    }
  },
  components: {
    DrawLine
  }
}
</script>

<style lang="scss" scoped>
.cover {
  position: absolute;
  width: 100%;
  height: 1000vh;
  background: rgba(20, 20, 20, 0.8);
  z-index: 1;
}
.renderPhone {
  position: fixed;
  width: 100%;
  height: 100vh;
  z-index: 0;
}
.intro {
  position: fixed;
  min-width: 100%;
  height: 100vh;
  color: white;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  z-index: 2;
  p {
    text-align: center;
    position: absolute;
    word-break: keep-all;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
    font-size: 4.5em;
    opacity: 0;
    margin: 0;
  }
}
.description {
  position: fixed;
  transform: translate(20%, -7%);
  width: 100%;
  height: 100vh;
  color: white;
  mix-blend-mode: difference;
  display: flex;
  flex-direction: column;
  justify-content: center;
  z-index: 2;
  .draw {
    position: relative;
    width: 20%;
    margin-bottom: 7%;
    z-index: 2;
  }
  p {
    position: relative;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
    font-size: 1.5em;
    opacity: 0;
    margin: 0;
  }
}
.maskrap {
  position: fixed;
  width: 100%;
  height: 100vh;
  z-index: 1;
  .mask {
    position: relative;
    transform: translate(-50%, -50%);
    top: 50%;
    left: 50%;
    width: 500%;
    height: 500%;
    background: white;
    clip-path: circle(0%);
  }
}
</style>
