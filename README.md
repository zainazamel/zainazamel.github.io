<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D Character Customization</title>
    <style>
        body { margin: 0; overflow: hidden; }
        #controls { position: absolute; top: 10px; left: 10px; z-index: 100; }
        select, button { margin: 5px; padding: 5px; }
    </style>
</head>
<body>

<div id="controls">
    <select id="poseSelector">
        <option value="pose1">Pose 1</option>
        <option value="pose2">Pose 2</option>
        <option value="pose3">Pose 3</option>
    </select>
    
    <select id="shirtSelector">
        <option value="shirt1">Shirt 1</option>
        <option value="shirt2">Shirt 2</option>
        <option value="shirt3">Shirt 3</option>
    </select>
    
    <select id="pantsSelector">
        <option value="pants1">Pants 1</option>
        <option value="pants2">Pants 2</option>
        <option value="pants3">Pants 3</option>
    </select>
    
    <button id="customizeCharacter">Customize Character</button>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r134/three.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/@takram/three-gltf-loader@1.0.2/index.min.js"></script>
<script>
    // Basic setup for Three.js
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
    const renderer = new THREE.WebGLRenderer();
    renderer.setSize(window.innerWidth, window.innerHeight);
    document.body.appendChild(renderer.domElement);

    // Lighting
    const ambientLight = new THREE.AmbientLight(0xffffff, 1);
    scene.add(ambientLight);

    // Load initial character model
    let currentModel;
    const loader = new THREE.GLTFLoader();

    function loadCharacter(pose, shirt, pants) {
        if (currentModel) {
            scene.remove(currentModel);
        }

        loader.load(`./models/${pose}.gltf`, function (gltf) {
            currentModel = gltf.scene;

            // Customize shirt and pants
            const shirtTexture = new THREE.TextureLoader().load(`./textures/${shirt}.jpg`);
            const pantsTexture = new THREE.TextureLoader().load(`./textures/${pants}.jpg`);
            
            currentModel.traverse((child) => {
                if (child.isMesh) {
                    if (child.name === 'Shirt') {
                        child.material.map = shirtTexture;
                    } else if (child.name === 'Pants') {
                        child.material.map = pantsTexture;
                    }
                }
            });

            scene.add(currentModel);
            currentModel.position.set(0, -1, 0);
        });
    }

    // Event listener for customization
    document.getElementById('customizeCharacter').addEventListener('click', function () {
        const pose = document.getElementById('poseSelector').value;
        const shirt = document.getElementById('shirtSelector').value;
        const pants = document.getElementById('pantsSelector').value;
        loadCharacter(pose, shirt, pants);
    });

    // Camera position
    camera.position.z = 3;

    // Animation loop
    function animate() {
        requestAnimationFrame(animate);
        if (currentModel) {
            currentModel.rotation.y += 0.01; // Rotate the model
        }
        renderer.render(scene, camera);
    }
    animate();

    // Responsive design
    window.addEventListener('resize', function () {
        const width = window.innerWidth;
        const height = window.innerHeight;
        renderer.setSize(width, height);
        camera.aspect = width / height;
        camera.updateProjectionMatrix();
    });

    // Initial load
    loadCharacter('pose1', 'shirt1', 'pants1');
</script>

</body>
</html>
