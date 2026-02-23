<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CLIMASHIELD 3D Prototype</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Three.js Library -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <!-- OrbitControls -->
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js"></script>
    
    <style>
        body { margin: 0; overflow: hidden; background-color: #0f172a; font-family: 'Inter', sans-serif; }
        #canvas-container { width: 100vw; height: 100vh; position: absolute; top: 0; left: 0; z-index: 1; }
        #ui-layer { position: absolute; top: 0; left: 0; width: 100%; height: 100%; z-index: 10; pointer-events: none; }
        .pointer-events-auto { pointer-events: auto; }
        
        /* Custom scrollbar for info panel */
        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: #334155; border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: #475569; }
    </style>
</head>
<body class="text-slate-200">

    <!-- 3D Canvas Container -->
    <div id="canvas-container"></div>

    <!-- UI Overlay -->
    <div id="ui-layer" class="flex flex-col md:flex-row justify-between p-6 h-full">
        
        <!-- Left Panel: Info -->
        <div class="pointer-events-auto w-full md:w-96 bg-slate-900/80 backdrop-blur-md border border-slate-700 rounded-2xl p-6 shadow-2xl flex flex-col h-fit max-h-full overflow-y-auto mb-6 md:mb-0">
            <div class="flex items-center gap-3 mb-4">
                <div class="w-10 h-10 rounded-full bg-blue-500/20 border border-blue-500 flex items-center justify-center text-blue-400">
                    <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M20.38 3.46 16 2a8 8 0 0 1-8 8 8 8 0 0 1-8-8l-4.38 1.46a2 2 0 0 0-1.28 2.5l3.29 10.3A2 2 0 0 0 6.53 18h10.94a2 2 0 0 0 1.9-1.28l3.29-10.3a2 2 0 0 0-1.28-2.5z"/></svg>
                </div>
                <div>
                    <h1 class="text-xl font-bold text-white tracking-wide">CLIMASHIELD</h1>
                    <p class="text-xs text-slate-400 uppercase tracking-wider">Smart Adaptive Uniform</p>
                </div>
            </div>
            
            <p class="text-sm text-slate-300 mb-6 leading-relaxed">
                A premium, smart uniform jacket for Merchant Navy and Army Officers. Automatically adapts to climate and provides visual weather alerts via a stealth smart collar.
            </p>

            <div class="space-y-4">
                <div class="border-l-2 border-blue-500 pl-3">
                    <h3 class="text-sm font-semibold text-white">Smart Collar Alerts</h3>
                    <p class="text-xs text-slate-400 mt-1">LED strip inside collar provides visual warnings without sound, keeping officers aware without distraction.</p>
                </div>
                <div class="border-l-2 border-emerald-500 pl-3">
                    <h3 class="text-sm font-semibold text-white">Adaptive Ventilation</h3>
                    <p class="text-xs text-slate-400 mt-1">Mechanical micro-vents open in heat for breathability and close in cold/rain to retain heat.</p>
                </div>
            </div>
            
            <div class="mt-8 text-xs text-slate-500 flex items-center gap-2">
                <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M5 22h14"/><path d="M5 2h14"/><path d="M17 22v-4.172a2 2 0 0 0-.586-1.414L12 12l-4.414 4.414A2 2 0 0 0 7 17.828V22"/><path d="M7 2v4.172a2 2 0 0 0 .586 1.414L12 12l4.414-4.414A2 2 0 0 0 17 6.172V2"/></svg>
                Drag to rotate 3D model • Scroll to zoom
            </div>
        </div>

        <!-- Right Panel: Controls -->
        <div class="pointer-events-auto w-full md:w-80 flex flex-col gap-3 justify-center">
            <h2 class="text-sm font-semibold text-slate-400 uppercase tracking-widest mb-2 text-center md:text-right">Environment Simulator</h2>
            
            <button onclick="setCondition('normal')" id="btn-normal" class="sim-btn relative group overflow-hidden rounded-xl bg-slate-800 border border-slate-700 p-4 transition-all hover:bg-slate-700 focus:outline-none focus:ring-2 focus:ring-blue-500">
                <div class="flex items-center justify-between relative z-10">
                    <div class="flex items-center gap-3">
                        <div class="w-3 h-3 rounded-full bg-blue-500 shadow-[0_0_10px_rgba(59,130,246,0.8)]"></div>
                        <span class="font-medium text-white">Normal / Hot</span>
                    </div>
                    <span class="text-xs text-slate-400">Vents Open</span>
                </div>
            </button>

            <button onclick="setCondition('warning')" id="btn-warning" class="sim-btn relative group overflow-hidden rounded-xl bg-slate-800 border border-slate-700 p-4 transition-all hover:bg-slate-700 focus:outline-none focus:ring-2 focus:ring-yellow-500">
                <div class="flex items-center justify-between relative z-10">
                    <div class="flex items-center gap-3">
                        <div class="w-3 h-3 rounded-full bg-yellow-500 shadow-[0_0_10px_rgba(234,179,8,0.8)]"></div>
                        <span class="font-medium text-white">Strong Wind</span>
                    </div>
                    <span class="text-xs text-slate-400">Vents Closed</span>
                </div>
            </button>

            <button onclick="setCondition('danger')" id="btn-danger" class="sim-btn relative group overflow-hidden rounded-xl bg-slate-800 border border-slate-700 p-4 transition-all hover:bg-slate-700 focus:outline-none focus:ring-2 focus:ring-red-500">
                <div class="flex items-center justify-between relative z-10">
                    <div class="flex items-center gap-3">
                        <div class="w-3 h-3 rounded-full bg-red-500 shadow-[0_0_10px_rgba(239,68,68,0.8)]"></div>
                        <span class="font-medium text-white">Storm / Danger</span>
                    </div>
                    <span class="text-xs text-slate-400">Sealed</span>
                </div>
            </button>

            <button onclick="setCondition('fog')" id="btn-fog" class="sim-btn relative group overflow-hidden rounded-xl bg-slate-800 border border-slate-700 p-4 transition-all hover:bg-slate-700 focus:outline-none focus:ring-2 focus:ring-white">
                <div class="flex items-center justify-between relative z-10">
                    <div class="flex items-center gap-3">
                        <div class="w-3 h-3 rounded-full bg-white shadow-[0_0_10px_rgba(255,255,255,0.8)]"></div>
                        <span class="font-medium text-white">Fog / Low Vis</span>
                    </div>
                    <span class="text-xs text-slate-400">Visibility Mode</span>
                </div>
            </button>
        </div>
    </div>

    <script>
        // --- THREE.JS SETUP ---
        let scene, camera, renderer, controls;
        let jacketGroup, collarMaterial, vents = [];
        
        // Animation Target States
        let targetCollarColor = new THREE.Color(0x3b82f6); // Start Blue
        let targetVentRotation = Math.PI / 4; // Start Open (45 degrees)
        let collarIntensity = 0; // For pulsing effect

        function init() {
            const container = document.getElementById('canvas-container');

            // Scene
            scene = new THREE.Scene();
            scene.fog = new THREE.FogExp2(0x0f172a, 0.02);

            // Camera
            camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 100);
            camera.position.set(0, 1, 8);

            // Renderer
            renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
            renderer.setSize(window.innerWidth, window.innerHeight);
            renderer.setPixelRatio(window.devicePixelRatio);
            // Add subtle bloom/glow setup by adjusting tone mapping
            renderer.toneMapping = THREE.ACESFilmicToneMapping;
            renderer.toneMappingExposure = 1.2;
            container.appendChild(renderer.domElement);

            // Controls
            controls = new THREE.OrbitControls(camera, renderer.domElement);
            controls.enableDamping = true;
            controls.dampingFactor = 0.05;
            controls.minDistance = 4;
            controls.maxDistance = 12;
            controls.target.set(0, 1, 0);

            // Lighting
            const ambientLight = new THREE.AmbientLight(0xffffff, 0.4);
            scene.add(ambientLight);

            const dirLight = new THREE.DirectionalLight(0xffffff, 0.8);
            dirLight.position.set(5, 5, 5);
            scene.add(dirLight);

            const backLight = new THREE.DirectionalLight(0x475569, 0.5);
            backLight.position.set(-5, 3, -5);
            scene.add(backLight);

            createJacket();

            // Resize handler
            window.addEventListener('resize', onWindowResize);

            // Set initial UI state
            updateUIState('normal');

            // Start Animation Loop
            animate();
        }

        function createJacket() {
            jacketGroup = new THREE.Group();
            scene.add(jacketGroup);

            // Jacket Colors (Military/Navy style)
            const fabricColor = 0x1e293b;
            const detailColor = 0x0f172a;

            // 1. Torso
            const torsoGeo = new THREE.CylinderGeometry(1.3, 1.2, 3.5, 32);
            // Flatten cylinder to look like a chest
            torsoGeo.scale(1, 1, 0.6); 
            const fabricMat = new THREE.MeshStandardMaterial({ 
                color: fabricColor, 
                roughness: 0.9, 
                metalness: 0.1 
            });
            const torso = new THREE.Mesh(torsoGeo, fabricMat);
            torso.position.y = 1;
            jacketGroup.add(torso);

            // 2. Shoulders/Arms (Abstract)
            const shoulderGeo = new THREE.SphereGeometry(1.3, 32, 32);
            shoulderGeo.scale(1.2, 0.5, 0.6);
            const shoulder = new THREE.Mesh(shoulderGeo, fabricMat);
            shoulder.position.y = 2.5;
            jacketGroup.add(shoulder);

            const leftArmGeo = new THREE.CylinderGeometry(0.4, 0.3, 3, 16);
            const leftArm = new THREE.Mesh(leftArmGeo, fabricMat);
            leftArm.position.set(-1.6, 0.5, 0);
            leftArm.rotation.z = Math.PI / 8;
            jacketGroup.add(leftArm);

            const rightArmGeo = new THREE.CylinderGeometry(0.4, 0.3, 3, 16);
            const rightArm = new THREE.Mesh(rightArmGeo, fabricMat);
            rightArm.position.set(1.6, 0.5, 0);
            rightArm.rotation.z = -Math.PI / 8;
            jacketGroup.add(rightArm);

            // 3. Central Zipper Line
            const zipperGeo = new THREE.BoxGeometry(0.05, 3.5, 0.1);
            const zipperMat = new THREE.MeshStandardMaterial({ color: 0x000000, roughness: 0.5 });
            const zipper = new THREE.Mesh(zipperGeo, zipperMat);
            zipper.position.set(0, 1, 0.8);
            jacketGroup.add(zipper);

            // 4. Smart Collar (Weather Alert System)
            const collarGeo = new THREE.CylinderGeometry(0.7, 0.8, 0.6, 32, 1, true);
            collarMaterial = new THREE.MeshStandardMaterial({ 
                color: 0x111111,
                emissive: targetCollarColor,
                emissiveIntensity: 2.0, // High intensity for glow
                side: THREE.DoubleSide,
                roughness: 0.2
            });
            const collar = new THREE.Mesh(collarGeo, collarMaterial);
            collar.position.y = 2.9;
            // Angle collar slightly
            collar.rotation.x = Math.PI / 16;
            jacketGroup.add(collar);
            
            // Inner neck block to hide hollowness
            const neckGeo = new THREE.CylinderGeometry(0.65, 0.65, 0.7, 32);
            const neckMat = new THREE.MeshStandardMaterial({ color: 0x000000 });
            const neck = new THREE.Mesh(neckGeo, neckMat);
            neck.position.y = 2.9;
            jacketGroup.add(neck);

            // 5. Adaptive Vents (Climate Control)
            const ventMat = new THREE.MeshStandardMaterial({ color: detailColor, roughness: 0.8 });
            const innerVentMat = new THREE.MeshStandardMaterial({ color: 0x333333, roughness: 1 }); // Mesh underneath

            // Create 3 vents on each side of the chest
            for(let side of [-1, 1]) {
                for(let i=0; i<3; i++) {
                    // Create a hole/backing for the vent
                    const holeGeo = new THREE.PlaneGeometry(0.6, 0.15);
                    const hole = new THREE.Mesh(holeGeo, innerVentMat);
                    hole.position.set(side * 0.5, 2.0 - (i * 0.4), 0.76);
                    // Curve plane slightly to match torso
                    hole.rotation.y = side * Math.PI / 12; 
                    jacketGroup.add(hole);

                    // Create the moving flap
                    const flapGroup = new THREE.Group();
                    
                    const flapGeo = new THREE.BoxGeometry(0.65, 0.2, 0.02);
                    const flap = new THREE.Mesh(flapGeo, ventMat);
                    // Offset mesh so it rotates from the top edge (hinge)
                    flap.position.y = -0.1;
                    flapGroup.add(flap);
                    
                    // Position the hinge group
                    flapGroup.position.set(side * 0.5, 2.1 - (i * 0.4), 0.77);
                    flapGroup.rotation.y = side * Math.PI / 12;
                    
                    jacketGroup.add(flapGroup);
                    vents.push(flapGroup);
                }
            }
        }

        // Handle Window Resize
        function onWindowResize() {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        }

        // Animation Loop
        function animate() {
            requestAnimationFrame(animate);

            // Smoothly interpolate collar color
            collarMaterial.emissive.lerp(targetCollarColor, 0.05);
            
            // Pulse effect for collar
            collarIntensity += 0.05;
            collarMaterial.emissiveIntensity = 1.5 + Math.sin(collarIntensity) * 0.5;

            // Smoothly animate vents opening/closing
            vents.forEach(vent => {
                // Hinge rotation on X axis
                vent.rotation.x += (targetVentRotation - vent.rotation.x) * 0.1;
            });

            // Slowly rotate jacket if controls aren't actively being used
            // jacketGroup.rotation.y += 0.002;

            controls.update();
            renderer.render(scene, camera);
        }

        // --- UI & LOGIC INTEGRATION ---
        window.setCondition = function(condition) {
            updateUIState(condition);

            if (condition === 'normal') {
                targetCollarColor.setHex(0x3b82f6); // Blue
                targetVentRotation = Math.PI / 3.5; // Open (Heat escape)
            } 
            else if (condition === 'warning') {
                targetCollarColor.setHex(0xeab308); // Yellow
                targetVentRotation = 0.05; // Mostly Closed (Wind resistance)
            } 
            else if (condition === 'danger') {
                targetCollarColor.setHex(0xef4444); // Red
                targetVentRotation = 0; // Fully Closed (Storm sealed)
            } 
            else if (condition === 'fog') {
                targetCollarColor.setHex(0xffffff); // White
                targetVentRotation = Math.PI / 8; // Partially open
            }
        }

        function updateUIState(activeId) {
            const buttons = document.querySelectorAll('.sim-btn');
            buttons.forEach(btn => {
                btn.classList.remove('ring-2', 'ring-offset-2', 'ring-offset-slate-900');
                btn.style.opacity = '0.6';
            });
            
            const activeBtn = document.getElementById(`btn-${activeId}`);
            if(activeBtn) {
                activeBtn.classList.add('ring-2', 'ring-offset-2', 'ring-offset-slate-900');
                activeBtn.style.opacity = '1';
                
                // Add ring color based on condition
                activeBtn.classList.remove('ring-blue-500', 'ring-yellow-500', 'ring-red-500', 'ring-white');
                if(activeId === 'normal') activeBtn.classList.add('ring-blue-500');
                if(activeId === 'warning') activeBtn.classList.add('ring-yellow-500');
                if(activeId === 'danger') activeBtn.classList.add('ring-red-500');
                if(activeId === 'fog') activeBtn.classList.add('ring-white');
            }
        }

        // Start application when DOM is ready
        window.onload = init;

    </script>
</body>
</html>
