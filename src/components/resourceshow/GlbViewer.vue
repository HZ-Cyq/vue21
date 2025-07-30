<template>
  <div id="app">
    <canvas ref="canvas" class="webgl-canvas"></canvas>
  </div>
</template>

<script>
import * as THREE from 'three'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'

export default {
  mounted() {
    this.initThree()
  },
  methods: {
    initThree() {
      const canvas = this.$refs.canvas

      // 场景
      const scene = new THREE.Scene()
      scene.background = new THREE.Color(0xeeeeee)

      // 渲染器
      const renderer = new THREE.WebGLRenderer({ canvas, antialias: true })
      renderer.setSize(window.innerWidth, window.innerHeight)

      // 相机
      const camera = new THREE.PerspectiveCamera(
        45,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      )
      camera.position.set(0, 2, 5) // 调整摄像机位置，让模型居中并完整可见

      // 控制器
      const controls = new OrbitControls(camera, renderer.domElement)
      controls.target.set(0, 1, 0) // 控制焦点往模型中部偏移
      controls.update()

      // 灯光
      const ambientLight = new THREE.AmbientLight(0xffffff, 0.7)
      scene.add(ambientLight)

      const directionalLight = new THREE.DirectionalLight(0xffffff, 0.6)
      directionalLight.position.set(5, 10, 7.5)
      scene.add(directionalLight)

      // 加载 glb 模型
      const loader = new GLTFLoader()
      loader.load(
        '/models/test.glb',
        (gltf) => {
          const model = gltf.scene
          scene.add(model)

          // 自动居中并缩放
          const box = new THREE.Box3().setFromObject(model)
          const size = box.getSize(new THREE.Vector3()).length()
          const center = box.getCenter(new THREE.Vector3())
          model.position.sub(center) // 居中
          
          // 根据模型大小调整相机远近（可选）
          const distance = size * 1.2
          camera.position.set(0, size * 0.4, distance)
          controls.target.set(0, 0, 0)
          controls.update()
        },
        undefined,
        (error) => {
          console.error('模型加载失败:', error)
        }
      )

      // 动画循环
      const animate = () => {
        requestAnimationFrame(animate)
        renderer.render(scene, camera)
      }
      animate()

      // 响应式尺寸
      window.addEventListener('resize', () => {
        camera.aspect = window.innerWidth / window.innerHeight
        camera.updateProjectionMatrix()
        renderer.setSize(window.innerWidth, window.innerHeight)
      })
    }
  }
}
</script>

<style>
.webgl-canvas {
  width: 100vw;
  height: 100vh;
  display: block;
}
</style>
