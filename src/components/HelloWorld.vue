<template>
    <div ref="threeCanvas" class="three-canvas"></div>
  </template>
  
  <script setup lang="js">
  import { onMounted, ref } from 'vue';
  import * as THREE from 'three';
  
  // 获取模板中的 div 元素引用
  const threeCanvas = ref(null);
  
  // 定义场景、相机和渲染器变量
  let scene, camera, renderer, cube, particles;
  
  onMounted(() => {
    // 初始化 Three.js 场景、相机和渲染器
    initThree();
    // 开始动画循环
    animate();
  });
  
  function initThree() {
    // 创建场景
    scene = new THREE.Scene();
  
    // 创建透视相机
    camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
    // 设置相机位置
    camera.position.z = 5;
  
    // 创建 WebGL 渲染器并设置大小
    renderer = new THREE.WebGLRenderer();
    renderer.setSize(window.innerWidth, window.innerHeight);
    // 将渲染器的输出附加到 div 元素中
    threeCanvas.value.appendChild(renderer.domElement);
  
    // 创建一个立方体几何体和基础材质
    const geometry = new THREE.BoxGeometry();
    const material = new THREE.MeshBasicMaterial({ color: 0xe33c64, wireframe: true });
    // 使用几何体和材质创建网格对象（立方体）
    cube = new THREE.Mesh(geometry, material);
    // 将立方体添加到场景中
    scene.add(cube);
  
    // 创建粒子几何体
    const particleCount = 2000; // 粒子数量
    const particlesGeometry = new THREE.BufferGeometry();
    const positions = new Float32Array(particleCount * 3);
    const sizes = new Float32Array(particleCount);
  
    for (let i = 0; i < particleCount; i++) {
      const distance = Math.random() * 10; // 随机距离
      positions[i * 3] = (Math.random() - 0.5) * distance; // x 坐标
      positions[i * 3 + 1] = (Math.random() - 0.5) * distance; // y 坐标
      positions[i * 3 + 2] = (Math.random() - 0.5) * distance; // z 坐标
      sizes[i] = Math.random() * 0.05 + 0.02; // 随机大小在 [0.02, 0.07] 之间
    }
  
    particlesGeometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));
    particlesGeometry.setAttribute('size', new THREE.BufferAttribute(sizes, 1));
  
    // 创建粒子材质
    const particleMaterial = new THREE.PointsMaterial({
      color: 0xff0000,
      sizeAttenuation: false, // 关闭粒子随距离衰减的大小变化
      alphaTest: 0.5,
      depthWrite: false,
      blending: THREE.AdditiveBlending,
      vertexColors: true,
      transparent: true,
      size: sizes[0] // 设置初始粒子大小
    });
  
    // 使用粒子几何体和材质创建粒子系统
    particles = new THREE.Points(particlesGeometry, particleMaterial);
    // 将粒子系统添加到场景中
    scene.add(particles);
  }
  
  function animate() {
    requestAnimationFrame(animate);
  
    // 旋转立方体
    cube.rotation.x += 0.01;
    cube.rotation.y += 0.01;
  
    // 渲染场景
    renderer.render(scene, camera);
  }
  </script>
  
  <style scoped>
  /* 确保画布全屏显示 */
  .three-canvas {
    width: 100%;
    height: 100vh;
  }
  </style>
  