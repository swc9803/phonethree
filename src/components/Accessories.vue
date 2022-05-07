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
  setup () {
    const accessoriesRender = ref()

    onMounted(() => {
      function accessoriesAni () {
        const fov = 75
        const aspect = accessoriesRender.value.clientWidth / accessoriesRender.value.clientHeight
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
          gltfLoader.load('/models/ipad/ipad.gltf', (model) => {
            const ipad = model.scene
            ipad.position.set(-0.1, -1.35, 7.5)
            ipad.rotation.set(0, 0.5, 0)
            group.add(ipad)
            ipad.format = THREE.RGBAFormat
          })
          // // airpods
          // gltfLoader.load('/models/airpods/airpods.gltf', (model) => {
          //   const airpods = model.scene
          //   airpods.position.set(20, -1.35, -35)
          //   airpods.rotation.set(0.3, -0.4, 0.3)
          //   group.add(airpods)
          //   airpods.format = THREE.RGBAFormat
          // })
          // // macbook
          // gltfLoader.load('/models/macbook/macbook.gltf', (model) => {
          //   const macbook = model.scene
          //   macbook.position.set(-0.5, -0.1, 8.9)
          //   macbook.rotation.set(0.6, 2, 0)
          //   group.add(macbook)
          //   macbook.format = THREE.RGBAFormat
          // })
          // // watch
          // gltfLoader.load('/models/watch/watch.gltf', (model) => {
          //   const watch = model.scene
          //   watch.position.set(-5.5, -0.1, -180)
          //   watch.rotation.set(1.5, 0, -0.5)
          //   group.add(watch)
          //   scene.add(group)
          //   watch.format = THREE.RGBAFormat
          // })

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
  height: 100vh;
  .accessories {
    width: 100%;
    height: 100%;
  }
}
</style>
