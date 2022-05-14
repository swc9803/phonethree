<template>
  <div class="accessoriesWrap">
    <div class="accessories" ref="accessoriesRender" />
  </div>
</template>

<script>
import { onMounted, ref } from 'vue'
import * as THREE from 'three'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'
import { DRACOLoader } from 'three/examples/jsm/loaders/DRACOLoader.js'
import gsap from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'
gsap.registerPlugin(ScrollTrigger)

export default {
  props: {
    cover: Object
  },
  setup (props) {
    const accessoriesRender = ref()

    onMounted(() => {
      function accessoriesAni () {
        const fov = 75
        const aspect = accessoriesRender.value.clientWidth / accessoriesRender.value.clientHeight
        const near = 0.1
        const far = 2000
        const camera = new THREE.PerspectiveCamera(fov, aspect, near, far)
        camera.position.set(0, 0, 100)

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
          renderer.setSize(accessoriesRender.value.clientWidth, accessoriesRender.value.clientHeight)
          renderer.setPixelRatio(window.devicePixelRatio)

          accessoriesRender.value.appendChild(renderer.domElement)

          // eslint-disable-next-line no-unused-vars
          function render () {
            renderer.render(scene, camera)
          }

          const gltfLoader = new GLTFLoader()

          const dracoLoader = new DRACOLoader()
          dracoLoader.setDecoderPath('https://raw.githubusercontent.com/mrdoob/three.js/dev/examples/js/libs/draco/')
          gltfLoader.setDRACOLoader(dracoLoader)
          const group = new THREE.Group()
          // ipad
          gltfLoader.load('/models/ipad/ipad.gltf', (model) => {
            const ipad = model.scene
            ipad.position.set(0, 0, 0)
            ipad.rotation.set(0.3, -2.5, 0)
            group.add(ipad)
            ipad.format = THREE.RGBAFormat
            gsap.set(ipad.scale, { x: 7, y: 7, z: 7 })
            gsap.to(ipad.rotation, { y: 50, duration: 100, ease: 'none', repeat: -1 })
          })
          // airpods
          gltfLoader.load('/models/airpods/airpods.gltf', (model) => {
            const airpods = model.scene
            airpods.position.set(-5.5, -1, 0)
            airpods.rotation.set(0.3, -0.4, 0.3)
            group.add(airpods)
            airpods.format = THREE.RGBAFormat
            gsap.set(airpods.scale, { x: 0.3, y: 0.3, z: 0.3 })
            gsap.to(airpods.rotation, { y: 70, duration: 100, ease: 'none', repeat: -1 })
          })
          // watch
          gltfLoader.load('/models/watch/watch.gltf', (model) => {
            const watch = model.scene
            watch.position.set(5.5, 0, 0)
            watch.rotation.set(1.5, 0, -0.5)
            group.add(watch)
            scene.add(group)
            watch.format = THREE.RGBAFormat
            gsap.set(watch.scale, { x: 0.06, y: 0.06, z: 0.06 })
            gsap.to(watch.rotation, { z: -50, duration: 100, ease: 'none', repeat: -1 })
          })
          const accessoriesScroll = gsap.timeline({
            scrollTrigger: {
              trigger: props.cover,
              start: '78% top',
              end: '79%',
              scrub: 1
            }
          })
          accessoriesScroll.fromTo(accessoriesRender.value, { opacity: 0, y: 100 }, { opacity: 1, y: 0 })
          animate()
        }

        function animate () {
          requestAnimationFrame(animate)
          renderer.render(scene, camera)
        }
        init()

        function onWindowResize () {
          camera.aspect = accessoriesRender.value.clientWidth / accessoriesRender.value.clientHeight
          camera.updateProjectionMatrix()
          renderer.setSize(accessoriesRender.value.clientWidth, accessoriesRender.value.clientHeight)
        }
        window.addEventListener('resize', onWindowResize)
      }
      accessoriesAni()
    })
    return {
      accessoriesRender
    }
  }
}
</script>

<style lang="scss" scoped>
.accessoriesWrap {
  position: fixed;
  transform: translate(0, -50%);
  top: 50%;
  width: 100%;
  height: 900vh;
  .accessories {
    width: 100%;
    height: 100%;
  }
}
</style>
