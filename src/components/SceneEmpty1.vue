<template>
  <div class="h-full w-full">
    <button>Add line</button>
    <div class="h-full w-full relative" ref="sceneWindow"></div>
  </div>
</template>

<script lang="ts">
import { Vue } from "vue-class-component";
import * as THREE from "three";

import {
  Camera,
  MathUtils,
  Mesh,
  NoBlending,
  Scene,
  WebGLRenderer
} from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import {
  CSS3DObject,
  CSS3DRenderer
} from "three/examples/jsm/renderers/CSS3DRenderer";
import ceilPowerOfTwo = MathUtils.ceilPowerOfTwo;

export default class SceneEmpty1 extends Vue {
  private renderer!: WebGLRenderer;

  private css3dRenderer!: CSS3DRenderer;

  private camera!: Camera;

  private scene!: Scene;

  private css3dScene!: Scene;

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
    camera.position.x = -155;
    camera.position.y = 200;
    camera.position.z = 155;

    const renderer: WebGLRenderer = new THREE.WebGLRenderer({
      antialias: true,
      alpha: true
    });
    renderer.setSize(width, height);
    renderer.shadowMap.type = THREE.PCFSoftShadowMap;
    renderer.shadowMap.enabled = true;
    sceneWindow.appendChild(renderer.domElement);

    const css3dRenderer: CSS3DRenderer = new CSS3DRenderer();
    css3dRenderer.setSize(width, height);
    css3dRenderer.domElement.style.position = "absolute";
    css3dRenderer.domElement.style.top = "0";
    css3dRenderer.shadowMap.type = THREE.PCFSoftShadowMap;
    css3dRenderer.shadowMap.enabled = true;

    sceneWindow.appendChild(css3dRenderer.domElement);

    this.renderer = renderer;
    this.css3dRenderer = css3dRenderer;
    this.camera = camera;
    this.scene = scene;
    this.css3dScene = new THREE.Scene();

    const controls = new OrbitControls(camera, css3dRenderer.domElement);
    // controls.enableDamping = true;
    // controls.dampingFactor = 0.05;
    controls.screenSpacePanning = false;
    controls.maxDistance = 500;
    controls.minDistance = 10;
    controls.maxPolarAngle = Math.PI / 2;
    this.controls = controls;

    scene.add(new THREE.AmbientLight(0x666666));

    const light = new THREE.DirectionalLight(0xdedede, 1);
    // light.position.set(0, 200, -100);
    light.position.set(0, 100, 0);
    light.position.multiplyScalar(1.3);
    light.castShadow = true;
    light.shadow.mapSize.width = 1024;
    light.shadow.mapSize.height = 1024;
    const d = 100;
    light.shadow.camera.left = -d;
    light.shadow.camera.right = d;
    light.shadow.camera.top = d;
    light.shadow.camera.bottom = -d;
    light.shadow.camera.far = 1000;
    // scene.add(light);
    this.css3dScene.add(light);

    const groundMesh = new THREE.Mesh(
      new THREE.PlaneBufferGeometry(20000, 20000),
      new THREE.MeshStandardMaterial({
        color: 0xffffff,
        flatShading: true
      })
    );
    groundMesh.receiveShadow = true;
    groundMesh.rotateX(-Math.PI / 2);
    // scene.add(groundMesh);

    const lineBasicMaterial = new THREE.LineBasicMaterial({ color: 0x00ffff });
    const points = [
      new THREE.Vector3(-10, 0, 0),
      new THREE.Vector3(0, 10, 0),
      new THREE.Vector3(10, 0, 0)
    ];

    const linesGeometry = new THREE.BufferGeometry().setFromPoints(points);
    scene.add(new THREE.Line(linesGeometry, lineBasicMaterial));

    const element = document.createElement("div");
    element.style.width = "100px";
    element.style.height = "100px";
    // element.style.opacity = "0.5";
    element.style.background = new THREE.Color(
      Math.random() * 0.21568627451 + 0.462745098039,
      Math.random() * 0.21568627451 + 0.462745098039,
      Math.random() * 0.21568627451 + 0.462745098039
    ).getStyle();
    element.textContent = "I am editable text!";
    element.setAttribute("contenteditable", "");
    const textElement = new CSS3DObject(element);
    textElement.position.x = 0;
    textElement.position.y = 10;
    textElement.position.z = 0;
    textElement.rotation.x = -Math.PI / 2;
    textElement.castShadow = true;
    textElement.receiveShadow = true;
    this.css3dScene.add(textElement);

    const element2 = document.createElement("div");
    element2.style.width = "100px";
    element2.style.height = "100px";
    // element.style.opacity = "0.5";
    element2.style.background = new THREE.Color(
      Math.random() * 0.21568627451 + 0.462745098039,
      Math.random() * 0.21568627451 + 0.462745098039,
      Math.random() * 0.21568627451 + 0.462745098039
    ).getStyle();
    element2.textContent = "I am editable text!";
    element2.setAttribute("contenteditable", "");
    const textElement2 = new CSS3DObject(element2);
    textElement2.position.x = 15;
    textElement2.position.y = 30;
    textElement2.position.z = 15;
    textElement2.rotation.x = -Math.PI / 2;
    textElement2.castShadow = true;
    textElement2.receiveShadow = true;
    this.css3dScene.add(textElement2);

    const geometry = new THREE.PlaneBufferGeometry(100, 100);
    const material = new THREE.MeshBasicMaterial({
      // color: 0x000000,
      // wireframe: true,
      // opacity: 0,
      side: THREE.DoubleSide
    });
    // material.color.set("black");
    // material.opacity = 0;
    // material.blending = NoBlending;
    const cube = new THREE.Mesh(geometry, material);
    cube.castShadow = true;
    cube.receiveShadow = true;
    cube.position.copy(textElement.position);
    cube.rotation.copy(textElement.rotation);
    this.cube = cube;
    // scene.add(cube);
  }

  animate() {
    requestAnimationFrame(this.animate);
    this.controls.update();
    this.css3dRenderer.render(this.css3dScene, this.camera);
    this.renderer.render(this.scene, this.camera);
    // this.controls.domElement =

    // this.cube.rotateX(-0.01);

    // this.cube.rotateY(0.01);

    // this.cube.translateX(0.01);
    // this.cube.translateY(0.01);
  }
}
</script>

<style scoped></style>
