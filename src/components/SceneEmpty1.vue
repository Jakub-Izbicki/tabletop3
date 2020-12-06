<template>
  <div class="h-full w-full">
    <button>Add line</button>
    <div class="h-full w-full" ref="sceneWindow"></div>
  </div>
</template>

<script lang="ts">
import { Vue } from "vue-class-component";
import * as THREE from "three";
import { Camera, Mesh, Scene, WebGLRenderer } from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";

export default class SceneEmpty1 extends Vue {
  private renderer!: WebGLRenderer;

  private camera!: Camera;

  private scene!: Scene;

  private controls!: OrbitControls;

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
    camera.position.x = -25;
    camera.position.y = 60;
    camera.position.z = 25;
    const renderer: WebGLRenderer = new THREE.WebGLRenderer({
      antialias: true
    });
    renderer.setSize(width, height);
    renderer.shadowMap.enabled = true;

    sceneWindow.appendChild(renderer.domElement);

    const geometry = new THREE.BoxGeometry(5, 20, 1);
    const material = new THREE.MeshPhongMaterial({ color: 0x00ff00 });
    const cube = new THREE.Mesh(geometry, material);
    cube.castShadow = true;
    // cube.receiveShadow = true;
    cube.rotateX(Math.PI / 2);
    cube.translateZ(-2);
    scene.add(cube);

    this.renderer = renderer;
    this.camera = camera;
    this.scene = scene;
    this.cube = cube;

    const controls = new OrbitControls(camera, renderer.domElement);
    // controls.enableDamping = true;
    // controls.dampingFactor = 0.05;
    controls.screenSpacePanning = false;
    controls.maxDistance = 500;
    controls.minDistance = 10;
    controls.maxPolarAngle = Math.PI / 2;

    scene.add(new THREE.AmbientLight(0x666666));

    const light = new THREE.DirectionalLight(0xdfebff, 1);
    // light.position.set(0, 200, -100);
    light.position.set(0, 200, 0);
    light.position.multiplyScalar(1.3);
    light.castShadow = true;
    light.shadow.mapSize.width = 1024;
    light.shadow.mapSize.height = 1024;
    const d = 300;
    light.shadow.camera.left = -d;
    light.shadow.camera.right = d;
    light.shadow.camera.top = d;
    light.shadow.camera.bottom = -d;
    light.shadow.camera.far = 1000;
    scene.add(light);

    const groundMesh = new THREE.Mesh(
      new THREE.PlaneBufferGeometry(20000, 20000),
      new THREE.MeshStandardMaterial({
        color: 0x55550f,
        flatShading: true
      })
    );
    groundMesh.receiveShadow = true;
    groundMesh.rotateX(-Math.PI / 2);
    scene.add(groundMesh);

    const lineBasicMaterial = new THREE.LineBasicMaterial({ color: 0x00ffff });
    const points = [
      new THREE.Vector3(-10, 0, 0),
      new THREE.Vector3(0, 10, 0),
      new THREE.Vector3(10, 0, 0)
    ];

    const linesGeometry = new THREE.BufferGeometry().setFromPoints(points);
    scene.add(new THREE.Line(linesGeometry, lineBasicMaterial));
  }

  animate() {
    requestAnimationFrame(this.animate);
    this.renderer.render(this.scene, this.camera);
    // this.controls.update();

    // this.cube.rotateX(-0.01);
    // this.cube.rotateY(0.01);

    // this.cube.translateX(0.01);
    // this.cube.translateY(0.01);
  }
}
</script>

<style scoped></style>
