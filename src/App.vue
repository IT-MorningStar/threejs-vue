<script setup lang="ts">
import * as THREE from "three";
import { DoubleSide } from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import { GUI } from "three/examples/jsm/libs/lil-gui.module.min.js";

// 创建一个场景
const scene = new THREE.Scene();

// 创建一个相机

const camera = new THREE.PerspectiveCamera(
  30, // 视角
  window.innerWidth / window.innerHeight, // 宽高比
  0.1, // 近平面
  1000 // 远平面
);

// 创建渲染器
const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

// 创建滑轨控制器
const controls = new OrbitControls(camera, renderer.domElement);
// controls.autoRotate = true;
controls.enableDamping = true;
// 创建几何体

const geometry = new THREE.BufferGeometry();
// 创建一个简单的矩形. 在这里我们左上和右下顶点被复制了两次。
// 因为在两个三角面片里，这两个顶点都需要被用到。
const vertices = new Float32Array([
  -1.0, -1.0, 1.0, 1.0, -1.0, 1.0, 1.0, 1.0, 1.0,

  1.0, 1.0, 1.0, -1.0, 1.0, 1.0, -1.0, -1.0, 1.0,
]);

geometry.setAttribute("position", new THREE.BufferAttribute(vertices, 3));

// const geometry = new THREE.BoxGeometry(1, 1, 1);

// 创建材质
const material = new THREE.MeshBasicMaterial({
  color: 0x00ff00,
  //   side: DoubleSide,
});

// 创建网格

const cube = new THREE.Mesh(geometry, material);

const parentCube = new THREE.Mesh(geometry, material);

parentCube.scale.set(1, 1, 1);
// parentCube.rotation.x = 0.5;

cube.position.set(3, 0, 0);
parentCube.position.set(-3, 0, 0);
cube.scale.set(1, 1, 1);

parentCube.add(cube);

// 将网格添加到场景中
scene.add(parentCube);

// 设置相机位置
camera.position.z = 5;
camera.position.x = 2;
camera.position.y = 2;
camera.lookAt(0, 0, 0);

// 增加x、y、z 的坐标轴辅助
const axesHelper = new THREE.AxesHelper(5);
scene.add(axesHelper);

// 渲染函数

function animate() {
  controls.update();
  requestAnimationFrame(animate);
  //   cube.rotation.x += 0.01;
  //   cube.rotation.y += 0.01;
  //   cube.rotation.z += 0.01;
  renderer.render(scene, camera);
}
// 渲染 ，渲染相关
animate();

let EventObject = {
  FullScreen: () => {
    document.body.requestFullscreen();
  },
  ExitFullScreen: () => {
    document.exitFullscreen();
  },
};

// 便捷调试工具
const gui = new GUI();
let folder = gui.addFolder("立方体");
folder.add(EventObject, "FullScreen").name("全屏");
folder.add(EventObject, "ExitFullScreen");
folder.add(cube.position, "x").min(-10).max(10).name("x-轴");
folder.add(cube.position, "y").min(-10).max(10).name("y-轴");
folder.add(cube.position, "z").min(-10).max(10).name("z-轴");
folder.add(material, "wireframe").name("是否线框模式");
let colorObj = { materialColor: "" };

gui
  .addColor(colorObj, "materialColor")
  .name("立方体颜色")
  .onChange((val: string) => {
    cube.material.color.set(val);
  });

window.addEventListener("ressize", () => {
  // 设置渲染器画布大小
  renderer.setSize(window.innerWidth, window.innerHeight);
  // 设置相机宽高比
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();
});
</script>

<template></template>

<style scoped>
</style>
