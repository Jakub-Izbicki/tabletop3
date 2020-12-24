<template>
  <div class="h-full w-full">
    <div class="h-full w-full relative" ref="sceneWindow"></div>
  </div>
</template>

<script lang="ts">
import { Vue } from "vue-class-component";
import * as THREE from "three";

import {
  Camera,
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
import Stats from "three/examples/jsm/libs/stats.module";

export default class SceneEmpty1 extends Vue {
  private renderer!: WebGLRenderer;

  // private css3dRenderer!: CSS3DRenderer;

  private camera!: Camera;

  private scene!: Scene;

  private css3dScene!: Scene;

  private controls!: OrbitControls;

  private stats!: Stats;

  mounted() {
    this.setUpRenderer();
    this.animate();
  }

  private setUpRenderer() {
    const sceneWindow = this.$refs.sceneWindow as HTMLElement;
    const width = sceneWindow.offsetWidth;
    const height = sceneWindow.offsetHeight;
    const scene = new THREE.Scene();

    const camera = new THREE.PerspectiveCamera(75, width / height, 0.1, 10000);
    camera.position.x = -90;
    camera.position.y = 200;
    camera.position.z = 105;

    const renderer: WebGLRenderer = new THREE.WebGLRenderer({
      alpha: true,
      antialias: true
    });
    renderer.setSize(width, height);
    renderer.domElement.style.position = "absolute";
    renderer.domElement.style.top = "0";
    renderer.shadowMap.type = THREE.PCFSoftShadowMap;
    renderer.shadowMap.enabled = true;

    // const css3dRenderer: CSS3DRenderer = new CSS3DRenderer();
    // css3dRenderer.setSize(width, height);
    // css3dRenderer.domElement.style.position = "absolute";
    // css3dRenderer.domElement.style.top = "0";

    this.renderer = renderer;
    // this.css3dRenderer = css3dRenderer;
    this.camera = camera;
    this.scene = scene;
    this.css3dScene = new THREE.Scene();

    sceneWindow.appendChild(renderer.domElement);
    // sceneWindow.appendChild(css3dRenderer.domElement);

    const controls = new OrbitControls(camera, this.renderer.domElement);
    // const controls = new OrbitControls(camera, this.css3dRenderer.domElement);
    controls.screenSpacePanning = false;
    controls.maxDistance = 2000;
    controls.minDistance = 10;
    controls.maxPolarAngle = Math.PI / 2;
    this.controls = controls;

    scene.add(new THREE.AmbientLight(0x666666));

    const light = new THREE.DirectionalLight(0xdedede, 1);
    light.position.set(0, 200, 0);
    light.shadow.bias = -0.001;
    light.position.multiplyScalar(1.3);
    light.castShadow = true;
    light.shadow.mapSize.width = 2048;
    light.shadow.mapSize.height = 2048;
    light.shadow.camera.left = -1000;
    light.shadow.camera.right = 1000;
    light.shadow.camera.top = 1000;
    light.shadow.camera.bottom = -1000;
    light.shadow.camera.far = 1000;
    scene.add(light);

    const lightHelper = new THREE.DirectionalLightHelper(light);
    scene.add(lightHelper);

    const cameraHelper = new THREE.CameraHelper(light.shadow.camera);
    scene.add(cameraHelper);

    const groundMesh = new THREE.Mesh(
      new THREE.PlaneBufferGeometry(20000, 20000),
      new THREE.MeshStandardMaterial({
        color: 0xffaaaa,
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

    // @ts-ignore
    this.stats = new Stats();
    this.stats.showPanel(0);
    sceneWindow.appendChild(this.stats.dom);

    const side = 10;

    [...new Array(side).keys()].forEach(i => {
      [...new Array(side).keys()].forEach(j => {
        this.createTextPlane((i * 110) - ((side * 110) / 2), 10, (j * 110) - ((side * 110) / 2));
      });
    });
  }

  animate() {
    requestAnimationFrame(this.animate);

    this.stats.begin();
    // this.controls.update();
    this.renderer.render(this.scene, this.camera);
    // this.css3dRenderer.render(this.css3dScene, this.camera);
    this.stats.end();
  }

  createTextPlane(x: number, y: number, z: number) {
    // const element = document.createElement("div");
    // element.style.width = "100px";
    // element.style.height = "100px";
    // element.style.background = new THREE.Color(
    //   Math.random() * 0.21568627451 + 0.462745098039,
    //   Math.random() * 0.21568627451 + 0.462745098039,
    //   Math.random() * 0.21568627451 + 0.462745098039
    // ).getStyle();
    // element.textContent = "Hello world";
    // element.setAttribute("contenteditable", "");
    // const textElement = new CSS3DObject(element);
    // textElement.position.x = x;
    // textElement.position.y = y;
    // textElement.position.z = z;
    // textElement.rotation.x = -Math.PI / 2;

    const geometry = new THREE.PlaneBufferGeometry(100, 100);
    const material = new THREE.MeshStandardMaterial({
      // wireframe: true,
      flatShading: true,
      side: THREE.DoubleSide
    });
    material.color.set("grey");
    // material.opacity = 0;
    // material.blending = NoBlending;
    const plane = new THREE.Mesh(geometry, material);
    plane.castShadow = true;
    plane.receiveShadow = true;
    plane.position.x = x;
    plane.position.y = y;
    plane.position.z = z;
    plane.rotation.x = -Math.PI / 2;

    // this.css3dScene.add(textElement);
    this.scene.add(plane);
  }
}
</script>

<style scoped></style>
