<script lang="ts">
  import { onDestroy, onMount } from 'svelte';
  import * as THREE from 'three';

  let canvas: HTMLCanvasElement;
  let resizeObserver: ResizeObserver;
  let geometries: Array<THREE.BufferGeometry> = [];
  let materials: Array<THREE.Material> = [];

  onMount(() => {
    resizeObserver = new ResizeObserver((entries) => {
      if (entries.length > 0) {
        const entry = entries[0];
        if (entry.contentBoxSize.length > 0) {
          const size = entry.contentBoxSize[0];

          const width = size.inlineSize;
          const height = size.blockSize;

          canvas.width = width;
          canvas.height = height;
          const scene = new THREE.Scene();
          const renderer = new THREE.WebGLRenderer({ canvas });
          renderer.setViewport(0, 0, width, height);
          const camera = new THREE.PerspectiveCamera(50, width / height, 1, 3000);
          camera.position.set(0, 0, 100);
          camera.lookAt(0, 0, 0);

          const starGeometry = new THREE.SphereGeometry(10, 32, 32);
          const starMaterial = new THREE.MeshStandardMaterial({ color: 0x049ef4 });
          const star = new THREE.Mesh(starGeometry, starMaterial);
          star.position.set(0, 0, 0);

          const light = new THREE.AmbientLight( 0x404040 ); // soft white light
          scene.add( light );

          geometries.push(starGeometry);
          materials.push(starMaterial);

          scene.add(star);
          renderer.render(scene, camera);
        }
      }
    });
    resizeObserver.observe(canvas.parentElement);
  });

  onDestroy(() => {
    while (geometries.length > 0) {
      geometries.pop().dispose();
    }
    while (materials.length > 0) {
      materials.pop().dispose();
    }
    resizeObserver?.disconnect();
  });
</script>

<canvas bind:this={canvas} />

<style>
  canvas {
    height: 100%;
  }
</style>
