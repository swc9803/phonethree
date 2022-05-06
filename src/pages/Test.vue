<template>
  <div class="cover" ref="cover" />
  <div class="renderPhone" ref="renderPhone" />
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
    const cover = ref()
    const renderPhone = ref()

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

          const toLoad = [
            {
              name: 'ipad',
              group: new THREE.Group(),
              file: 'ipad/scene.gltf'
            },
            {
              name: 'scene',
              group: new THREE.Group(),
              file: 'phone/phone.gltf'
            }
          ]
          const models = {}
          const clones = {}
          toLoad.forEach((item) => {
            gltfLoader.load(item.file, (model) => {
              model.scene.traverse((child) => {
                if (child instanceof THREE.Mesh) {
                  child.receiveShadow = true
                  child.castShadow = true
                }
              })
              item.group.add(model.scene)
              models[item.name] = item.group
              const clone = item.group.clone()
              clones[item.name] = clone
              clone.position.set(-0.1, -1.35, 7.5)
            })
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
      renderPhone
    }
  }
}
</script>

<style lang="scss" scoped>
.cover {
  position: absolute;
  width: 100%;
  height: 400vh;
  // background: rgba(20, 20, 20, 0.8);
  z-index: 1;
}
.renderPhone {
  position: fixed;
  width: 100%;
  height: 100vh;
  z-index: 0;
}
</style>
