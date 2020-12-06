<template>
  <div class="h-full w-full" ref="sceneWindow"></div>
</template>

<script lang="ts">
import { Vue } from "vue-class-component";
import * as THREE from "three";
import { Camera, Mesh, Scene, WebGLRenderer } from "three";

export default class SceneEmpty1 extends Vue {
  private renderer!: WebGLRenderer;

  private camera!: Camera;

  private scene!: Scene;

  private cube!: Mesh;

  mounted() {
    this.setUpRenderer();
    this.animate();
  }

  private setUpRenderer() {
    const sceneWindow = this.$refs.sceneWindow as HTMLElement;
    const width = sceneWindow.offsetWidth;
    const height = sceneWindow.offsetHeight;
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(75, width / height, 0.1, 1000);
    camera.position.z = 5;
    const renderer: WebGLRenderer = new THREE.WebGLRenderer();
    renderer.setSize(width, height);

    sceneWindow.appendChild(renderer.domElement);

    const geometry = new THREE.BoxGeometry();
    const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
    const cube = new THREE.Mesh(geometry, material);
    scene.add(cube);

    this.renderer = renderer;
    this.camera = camera;
    this.scene = scene;
    this.cube = cube;
  }

  animate() {
    requestAnimationFrame(this.animate);
    this.renderer.render(this.scene, this.camera);
    this.cube.rotateX(0.01);
    this.cube.rotateY(0.01);
  }
}
</script>

<style scoped></style>
