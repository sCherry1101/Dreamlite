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

    const side = 5;
    const half = side / 2;

    const squareShape = new THREE.Shape();

    squareShape.moveTo(-half, -half);
    squareShape.lineTo(half, -half);
    squareShape.lineTo(half, half);
    squareShape.lineTo(-half, half);
    squareShape.closePath();

    const squareGeometry = new THREE.ShapeGeometry(squareShape);

    const squareMaterial = new THREE.MeshBasicMaterial({
      color: 0x7c6cff,
      transparent: true,
      opacity: 0.18,
      side: THREE.DoubleSide
    });

    const square = new THREE.Mesh(
      squareGeometry,
      squareMaterial
    );

    group.add(square);

    function createLine(points) {
      const geometry =
        new THREE.BufferGeometry().setFromPoints(points);

      const material = new THREE.LineBasicMaterial({
        color: 0xffffff
      });

      const line = new THREE.Line(
        geometry,
        material
      );

      group.add(line);

      return line;
    }

    createLine([
      new THREE.Vector3(-half, -half, 0.01),
      new THREE.Vector3(half, -half, 0.01),
      new THREE.Vector3(half, half, 0.01),
      new THREE.Vector3(-half, half, 0.01),
      new THREE.Vector3(-half, -half, 0.01)
    ]);

    const offset = 0.7;

    createLine([
      new THREE.Vector3(
        -half,
        -half - offset,
        0.02
      ),
      new THREE.Vector3(
        half,
        -half - offset,
        0.02
      )
    ]);

    createLine([
      new THREE.Vector3(
        half + offset,
        -half,
        0.02
      ),
      new THREE.Vector3(
        half + offset,
        half,
        0.02
      )
    ]);

    square.scale.set(0.001, 0.001, 0.001);

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

      square.scale.set(
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

<div class="square-container">
  <div
    class="canvas"
    bind:this={container}
  ></div>

  <div class="formulas">
    <div class="formula perimeter">
      <span class="label">Perimeter</span>
      <span>
        P = 4 × <span class="side">a</span>
      </span>
    </div>

    <div class="formula area">
      <span class="label">Area</span>
      <span>
        A = <span class="side">a²</span>
      </span>
    </div>
  </div>
</div>

<style>
  .square-container {
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
    .square-container {
      height: 450px;
    }

    .formulas {
      flex-direction: column;
      gap: 0.4rem;
      font-size: 1.1rem;
    }
  }
</style>
