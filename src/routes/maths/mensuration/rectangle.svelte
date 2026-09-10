<script>
  import { onMount } from 'svelte';
  import * as THREE from 'three';

  let container;

  onMount(() => {
    const scene = new THREE.Scene();

    const width = container.clientWidth;
    const height = container.clientHeight;

    const camera = new THREE.OrthographicCamera(
      -10,
      10,
      7,
      -7,
      0.1,
      100
    );

    camera.position.z = 10;

    const renderer = new THREE.WebGLRenderer({
      antialias: true,
      alpha: true
    });

    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    renderer.setSize(width, height);

    container.appendChild(renderer.domElement);

    const group = new THREE.Group();
    scene.add(group);

    const widthValue = 7;
    const heightValue = 4;

    const halfWidth = widthValue / 2;
    const halfHeight = heightValue / 2;

    const rectangleShape = new THREE.Shape();

    rectangleShape.moveTo(-halfWidth, -halfHeight);
    rectangleShape.lineTo(halfWidth, -halfHeight);
    rectangleShape.lineTo(halfWidth, halfHeight);
    rectangleShape.lineTo(-halfWidth, halfHeight);
    rectangleShape.closePath();

    const rectangleGeometry =
      new THREE.ShapeGeometry(rectangleShape);

    const rectangleMaterial = new THREE.MeshBasicMaterial({
      color: 0x7c6cff,
      transparent: true,
      opacity: 0.18,
      side: THREE.DoubleSide
    });

    const rectangle = new THREE.Mesh(
      rectangleGeometry,
      rectangleMaterial
    );

    group.add(rectangle);

    function createLine(points, color = 0xffffff) {
      const geometry =
        new THREE.BufferGeometry().setFromPoints(points);

      const material = new THREE.LineBasicMaterial({
        color
      });

      const line = new THREE.Line(
        geometry,
        material
      );

      group.add(line);

      return line;
    }

    createLine([
      new THREE.Vector3(-halfWidth, -halfHeight, 0.01),
      new THREE.Vector3(halfWidth, -halfHeight, 0.01),
      new THREE.Vector3(halfWidth, halfHeight, 0.01),
      new THREE.Vector3(-halfWidth, halfHeight, 0.01),
      new THREE.Vector3(-halfWidth, -halfHeight, 0.01)
    ]);

    const offset = 0.7;

    createLine([
      new THREE.Vector3(
        -halfWidth,
        -halfHeight - offset,
        0.02
      ),
      new THREE.Vector3(
        halfWidth,
        -halfHeight - offset,
        0.02
      )
    ]);

    createLine([
      new THREE.Vector3(
        halfWidth + offset,
        -halfHeight,
        0.02
      ),
      new THREE.Vector3(
        halfWidth + offset,
        halfHeight,
        0.02
      )
    ]);

    rectangle.scale.set(0.001, 0.001, 0.001);

    const start = performance.now();

    function animate(time) {
      requestAnimationFrame(animate);

      const elapsed = (time - start) / 1000;

      const progress = Math.min(
        Math.max(elapsed / 0.8, 0),
        1
      );

      const eased =
        1 - Math.pow(1 - progress, 3);

      rectangle.scale.set(
        eased,
        eased,
        eased
      );

      renderer.render(scene, camera);
    }

    animate(start);

    function resize() {
      const newWidth = container.clientWidth;
      const newHeight = container.clientHeight;

      const aspect = newWidth / newHeight;
      const viewSize = 7;

      camera.left = -viewSize * aspect;
      camera.right = viewSize * aspect;
      camera.top = viewSize;
      camera.bottom = -viewSize;

      camera.updateProjectionMatrix();

      renderer.setSize(
        newWidth,
        newHeight
      );
    }

    resize();

    window.addEventListener('resize', resize);

    return () => {
      window.removeEventListener('resize', resize);

      renderer.dispose();

      container.removeChild(
        renderer.domElement
      );
    };
  });
</script>

<div class="rectangle-container">
  <div
    class="canvas"
    bind:this={container}
  ></div>

  <div class="formulas">
    <div class="formula perimeter">
      <span class="label">Perimeter</span>
      <span>
        P =
        <span class="side">2(l + w)</span>
      </span>
    </div>

    <div class="formula area">
      <span class="label">Area</span>
      <span>
        A =
        <span class="side">l × w</span>
      </span>
    </div>
  </div>
</div>

<style>
  .rectangle-container {
    position: relative;
    width: 100%;
    height: 600px;
    overflow: hidden;
  }

  .canvas {
    width: 100%;
    height: 100%;
  }

  .formulas {
    position: absolute;
    bottom: 20px;
    left: 50%;
    transform: translateX(-50%);

    display: flex;
    align-items: center;
    gap: 1rem;

    padding: 0.7rem 1.2rem;

    border: 1px solid var(--border);
    border-radius: 0.8rem;

    background: var(--bg-secondary);
    color: var(--text-primary);

    font-size: 1.25rem;
    font-weight: 600;
  }

  .formula {
    display: flex;
    align-items: center;
    gap: 0.45rem;
  }

  .label {
    font-size: 0.9rem;
    opacity: 0.65;
    font-weight: 500;
  }

  .side {
    color: #7c6cff;
  }

  @media (max-width: 600px) {
    .rectangle-container {
      height: 450px;
    }

    .formulas {
      flex-direction: column;
      gap: 0.4rem;
      font-size: 1.1rem;
    }
  }
</style>
