<script lang="ts">
	import { onMount, onDestroy } from 'svelte';
	import * as THREE from 'three';
	import { OBJLoader } from 'three/examples/jsm/loaders/OBJLoader.js';
	import gsap from 'gsap';

	let ufo: THREE.Object3D;
	let audio: HTMLAudioElement;
	let renderer: THREE.WebGLRenderer;

	onMount(() => {
		audio = new Audio('/sounds/laugh.ogg');

		const scene = new THREE.Scene();
		const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
		renderer = new THREE.WebGLRenderer({ alpha: true });
		renderer.setSize(window.innerWidth, window.innerHeight / 2);
		renderer.domElement.style.position = 'absolute';
		renderer.domElement.style.top = '0';
		renderer.domElement.style.left = '0';
		document.body.appendChild(renderer.domElement);

		const loader = new OBJLoader();
		loader.load('/UFO.obj', (object) => {
			ufo = object;
			ufo.traverse((child) => {
				if (child instanceof THREE.Mesh) {
					child.material = new THREE.MeshBasicMaterial({ color: 0x000000 }); // Black color
				}
			});
			const randomScale = 0.1 + Math.random() * 0.5; // Random scale between 0.1 and 0.6
			ufo.scale.set(randomScale, randomScale, randomScale);
			ufo.position.set(0, 1, -5);
			scene.add(ufo);
			animateUFO();
			moveUFO();
			journeyUFO();
			adjustSizeForMobile();
		});

		camera.position.z = 5;

		const animateUFO = () => {
			gsap.to(ufo.position, {
				y: 1.5,
				duration: 1,
				repeat: -1,
				yoyo: true,
				ease: 'power1.inOut'
			});
		};

		const moveUFO = () => {
			gsap.to(ufo.position, {
				x: 5,
				duration: 2,
				repeat: -1,
				yoyo: true,
				ease: 'power1.inOut'
			});
		};

		const journeyUFO = () => {
			gsap.to(ufo.position, {
				x: 10,
				duration: 5,
				repeat: -1,
				yoyo: true,
				ease: 'power1.inOut',
				startAt: { x: -10 }
			});
		};

		const adjustSizeForMobile = () => {
			if (window.innerWidth <= 768) {
				ufo.scale.set(0.2, 0.2, 0.2);
			}
		};

		const animate = () => {
			requestAnimationFrame(animate);
			renderer.render(scene, camera);
		};

		const handleUFOClick = () => {
			// Play sound effect
			audio.play();

			// Scale down, then up, then back to normal
			gsap.timeline()
				.to(ufo.scale, {
					duration: 0.5,
					x: -0.4,
					y: -0.4,
					z: -0.4,
					ease: 'power1.inOut'
				})
				.to(ufo.scale, {
					duration: 0.5,
					x: 0.6,
					y: 0.6,
					z: 0.6,
					ease: 'power1.inOut'
				})
				.to(ufo.scale, {
					duration: 0.5,
					x: 0.4,
					y: 0.4,
					z: 0.4,
					ease: 'power1.inOut'
				});
			// Rotate the UFO
			gsap.to(ufo.rotation, {
				duration: 1,
				y: ufo.rotation.y + Math.PI * 2,
				ease: 'power1.inOut'
			});
		};

		const handleClick = (event: MouseEvent) => {
			const mouse = new THREE.Vector2();
			mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
			mouse.y = -(event.clientY / (window.innerHeight / 2)) * 2 + 1;

			const raycaster = new THREE.Raycaster();
			raycaster.setFromCamera(mouse, camera);

			const intersects = raycaster.intersectObject(ufo, true);
			if (intersects.length > 0) {
				handleUFOClick();
			}
		};

		// Event listener for clicking on the UFO
		document.addEventListener('click', handleClick);

		animate();

		onDestroy(() => {
			document.removeEventListener('click', handleClick);
			document.body.removeChild(renderer.domElement);
			renderer.dispose();
		});
	});
</script>