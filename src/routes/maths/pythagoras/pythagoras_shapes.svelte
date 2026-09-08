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

    const A = new THREE.Vector3(-4, -2, 0);
    const B = new THREE.Vector3(4, -2, 0);
    const C = new THREE.Vector3(-4, 4, 0);
    const triangleShape = new THREE.Shape();
    triangleShape.moveTo(A.x, A.y);
    triangleShape.lineTo(B.x, B.y);
    triangleShape.lineTo(C.x, C.y);
    triangleShape.closePath();

    const triangleGeometry = new THREE.ShapeGeometry(triangleShape);
    const triangleMaterial = new THREE.MeshBasicMaterial({
      color: 0x7c6cff,
      transparent: true,
      opacity: 0.18,
      side: THREE.DoubleSide
    });

    const triangle = new THREE.Mesh(
      triangleGeometry,
      triangleMaterial
    );

    group.add(triangle);

   

    function createSquare(P1, P2, color) {
      const dx = P2.x - P1.x;
      const dy = P2.y - P1.y;

      const length = Math.sqrt(dx * dx + dy * dy);

     
      let nx = -dy / length;
      let ny = dx / length;

      
      nx *= -1;
      ny *= -1;

      const P3 = new THREE.Vector3(
        P2.x + nx * length,
        P2.y + ny * length,
        0
      );

      const P4 = new THREE.Vector3(
        P1.x + nx * length,
        P1.y + ny * length,
        0
      );

      const shape = new THREE.Shape();

      shape.moveTo(P1.x, P1.y);
      shape.lineTo(P2.x, P2.y);
      shape.lineTo(P3.x, P3.y);
      shape.lineTo(P4.x, P4.y);
      shape.closePath();

      const geometry = new THREE.ShapeGeometry(shape);

      const material = new THREE.MeshBasicMaterial({
        color,
        transparent: true,
        opacity: 0.16,
        side: THREE.DoubleSide
      });

      const mesh = new THREE.Mesh(geometry, material);

      group.add(mesh);

      return mesh;
    }

 
    const squareA = createSquare(
      A,
      B,
      0x4ade80
    );

    const squareB = createSquare(
      C,
      A,
      0xfacc15
    );

    
    const squareC = createSquare(
      B,
      C,
      0xf87171
    );

  

    function createLine(points) {
      const geometry = new THREE.BufferGeometry().setFromPoints(points);

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

    createLine([A, B, C, A]);
    createLine([
      new THREE.Vector3(
        A.x + (B.x - A.x),
        A.y + (B.y - A.y),
        0
      ),
      B
    ]);

    const objects = [
      triangle,
      squareA,
      squareB,
      squareC
    ];

    objects.forEach((object) => {
      object.scale.set(0.001, 0.001, 0.001);
    });

    let start = performance.now();

    function animate(time) {
      requestAnimationFrame(animate);

      const elapsed = (time - start) / 1000;

      objects.forEach((object, index) => {
        const delay = index * 0.35;
        const progress = Math.min(
          Math.max((elapsed - delay) / 0.8, 0),
          1
        );

        const eased =
          1 - Math.pow(1 - progress, 3);

        object.scale.set(
          eased,
          eased,
          eased
        );
      });

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
      container.removeChild(renderer.domElement);
    };
  });
</script>

<div class="pythagoras-container">
  <div class="canvas" bind:this={container}></div>

  <div class="formula">
    <span class="a">a²</span>
    <span>+</span>
    <span class="b">b²</span>
    <span>=</span>
    <span class="c">c²</span>
  </div>
</div>

<style>
  .pythagoras-container {
    position: relative;
    width: 100%;
    height: 600px;
    overflow: hidden;
  }

  .canvas {
    width: 100%;
    height: 100%;
  }

  .formula {
    position: absolute;
    bottom: 20px;
    left: 50%;
    transform: translateX(-50%);

    display: flex;
    align-items: center;
    gap: 0.6rem;

    padding: 0.7rem 1.2rem;

    border: 1px solid var(--border);
    border-radius: 0.8rem;

    background: var(--bg-secondary);
    color: var(--text-primary);

    font-size: 1.4rem;
    font-weight: 600;
  }

  .a {
    color: #4ade80;
  }

  .b {
    color: #facc15;
  }

  .c {
    color: #f87171;
  }

  @media (max-width: 600px) {
    .pythagoras-container {
      height: 450px;
    }

    .formula {
      font-size: 1.1rem;
    }
  }
</style>

