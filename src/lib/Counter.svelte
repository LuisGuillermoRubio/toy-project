<script>
  // @ts-nocheck

  import * as THREE from "three";
  import Stats from 'three/addons/libs/stats.module.js';

  let SCREEN_WIDTH = window.innerWidth;
  let SCREEN_HEIGHT = window.innerHeight;
  let aspect = SCREEN_WIDTH / SCREEN_HEIGHT;

  let container, stats;
  let camera, scene, renderer, mesh;
  let cameraRig, activeCamera, activeHelper;
  let cameraPerspective, cameraOrtho;
  let cameraPerspectiveHelper, cameraOrthoHelper;
  const frustumSize = 1000;

  init()
  animate()

  function init() {
    container = document.createElement("div");
    setTimeout(() => document.querySelector('main').appendChild(container), 1000) 

    scene = new THREE.Scene();

    //

    camera = new THREE.OrthographicCamera(150, 1 * aspect, 1, 1000);
    camera.position.z = 5500;

    //
    cameraOrtho = new THREE.OrthographicCamera(
      (0.5 * frustumSize * aspect) / -2,
      (0.5 * frustumSize * aspect) / 2,
      frustumSize / 2,
      frustumSize / -2,
      150,
      3000
    );

    cameraOrthoHelper = new THREE.CameraHelper(cameraOrtho);
    scene.add(cameraOrthoHelper);

    //

    activeCamera = cameraOrtho;
    activeHelper = cameraOrthoHelper;

    // counteract different front orientation of cameras vs rig

    cameraOrtho.rotation.y = Math.PI;

    cameraRig = new THREE.Group();

    cameraRig.add(cameraOrtho);

    scene.add(cameraRig);

    //

    mesh = new THREE.Mesh(
      new THREE.SphereGeometry(180,24, 16),
      new THREE.MeshBasicMaterial({ color: 0xffffff, wireframe: true })
    );
    scene.add(mesh);

    const mesh2 = new THREE.Mesh(
      new THREE.SphereGeometry(50, 16, 8),
      new THREE.MeshBasicMaterial({ color: 0x00ff00, wireframe: true })
    );
    mesh2.position.y = 150;
    mesh.add(mesh2);

    const mesh3 = new THREE.Mesh(
      new THREE.SphereGeometry(5, 16, 8),
      new THREE.MeshBasicMaterial({ color: 0x0000ff, wireframe: true })
    );
    mesh3.position.z = 150;
    cameraRig.add(mesh3);

    //

    const geometry = new THREE.BufferGeometry();
    const vertices = [];

    for (let i = 0; i < 10000; i++) {
      vertices.push(THREE.MathUtils.randFloatSpread(2000)); // x
      vertices.push(THREE.MathUtils.randFloatSpread(2000)); // y
      vertices.push(THREE.MathUtils.randFloatSpread(2000)); // z
    }

    geometry.setAttribute(
      "position",
      new THREE.Float32BufferAttribute(vertices, 3)
    );

    const particles = new THREE.Points(
      geometry,
      new THREE.PointsMaterial({ color: 0x888888 })
    );
    scene.add(particles);

    //

    renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setPixelRatio(window.devicePixelRatio);
    renderer.setSize(SCREEN_WIDTH/1.75, SCREEN_HEIGHT/1.75);
    container.appendChild(renderer.domElement);

    renderer.autoClear = false;

    //

    stats = new Stats();
    container.appendChild(stats.dom);

    //

    window.addEventListener("resize", onWindowResize);
    // document.addEventListener( 'keydown', onKeyDown );

    
  }

  function onWindowResize() {
      SCREEN_WIDTH = window.innerWidth;
      SCREEN_HEIGHT = window.innerHeight;
      aspect = SCREEN_WIDTH / SCREEN_HEIGHT;

      renderer.setSize(SCREEN_WIDTH, SCREEN_HEIGHT);

      camera.aspect = 0.5 * aspect;
      camera.updateProjectionMatrix();

      cameraOrtho.left = (-0.5 * frustumSize * aspect) / 2;
      cameraOrtho.right = (0.5 * frustumSize * aspect) / 2;
      cameraOrtho.top = frustumSize / 2;
      cameraOrtho.bottom = -frustumSize / 2;
      cameraOrtho.updateProjectionMatrix();
    }

    function animate() {
      requestAnimationFrame(animate);

      render();
      stats.update();
    }

    function render() {
      const r = Date.now() * 0.0005;

      mesh.position.x = 700 * Math.cos(r);
      mesh.position.z = 700 * Math.sin(r);
      mesh.position.y = 700 * Math.sin(r);

      mesh.children[0].position.x = 70 * Math.cos(2 * r);
      mesh.children[0].position.z = 70 * Math.sin(r);


        cameraOrtho.far = mesh.position.length();
        cameraOrtho.updateProjectionMatrix();

        cameraOrthoHelper.update();
        cameraOrthoHelper.visible = true;

        // cameraPerspectiveHelper.visible = false;
      

      cameraRig.lookAt(mesh.position);

      renderer.clear();

      activeHelper.visible = false;

      renderer.setViewport(250, -50, SCREEN_WIDTH/2 , SCREEN_HEIGHT/1.5);
      renderer.render(scene, activeCamera);

      activeHelper.visible = true;

      renderer.setViewport(
        SCREEN_WIDTH,
        0,
        SCREEN_WIDTH,
        SCREEN_HEIGHT
      );
      renderer.render(scene, camera);
    }
  
</script>

<div id="three">
  <h2>CRO Testing page</h2>
  <button>Click me</button>
</div>

