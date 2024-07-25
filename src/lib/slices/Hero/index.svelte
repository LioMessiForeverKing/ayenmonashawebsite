<script lang="ts">
	import { onMount } from 'svelte';
	import type { Content } from '@prismicio/client';
	import Scene from './Scene.svelte';
	import gsap from 'gsap';

	export let slice: Content.HeroSlice;

	const first_name_letters = slice.primary.first_name?.split('') ?? '';
	const last_name_letters = slice.primary.last_name?.split('') ?? '';

	onMount(() => {
		const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
		if (prefersReducedMotion) {
			gsap.to('.name-animation', { opacity: 1 });
			gsap.to('.job-title', { opacity: 1 });
			return;
		}

		const colors = ['red', 'orange', 'green', 'blue', 'purple', 'pink'];

		const tl = gsap.timeline();

		tl.fromTo(
			'.name-animation',
			{
				y: -100,
				opacity: 0,
				rotate: -10
			},
			{
				y: 0,
				rotate: 0,
				opacity: 1,
				ease: 'bounce.out',
				duration: 1,
				transformOrigin: 'center bottom',
				delay: 0.5,
				stagger: {
					each: 0.1,
					from: 'random'
				}
			}
		);

		tl.fromTo(
			'.job-title',
			{
				x: '100%',
				opacity: 0,
			},
			{
				x: '-100%',
				opacity: 1,
				duration: 10,
				ease: 'linear',
				repeat: -1,
				onRepeat: () => {
					gsap.set('.job-title', { x: '100%' });
				}
			}
		);

		gsap.to('.name-animation', {
			color: () => colors[Math.floor(Math.random() * colors.length)],
			duration: 0.5,
			repeat: -1,
			delay: 0.5,
			repeatDelay: 4.5,
			stagger: {
				each: 0.1
			}
		});
	});
</script>

<style>
	.red-glow {
		background: radial-gradient(
			circle at top center,
			rgb(255, 0, 0) 0%,
			hsla(38, 91%, 46%, 0.3) 30%,
			hsla(38, 91%, 46%, 0) 50%,
			hsla(257, 77%, 24%, 0) 100%
		);
		animation: flicker 1s infinite;
	}

	@keyframes flicker {
		0%, 100% {
			background: radial-gradient(
				circle at top center,
				rgb(255, 0, 0) 0%,
				hsla(38, 91%, 46%, 0.3) 30%,
				hsla(38, 91%, 46%, 0) 50%,
				hsla(257, 77%, 24%, 0) 100%
			);
			color: transparent;
		}
		50% {
			background: radial-gradient(
				circle at top center,
				rgb(255, 0, 0) 0%,
				hsla(38, 91%, 46%, 0) 30%,
				hsla(38, 91%, 46%, 0) 50%,
				hsla(257, 77%, 24%, 0) 100%
			);
			color: #eeeeee;
		}
	}

	.name-animation {
		display: inline-block;
		transition: color 0.5s ease-in-out, text-shadow 0.5s ease-in-out;
	}

	.job-title {
		position: relative;
		white-space: nowrap;
		overflow: hidden;
		display: inline-block;
	}
</style>

<section
	data-slice-type={slice.slice_type}
	data-slice-variation={slice.variation}
	class="px-4 md:px-6 red-glow"
>
	<div class="mx-auto w-full max-w-7xl">
		<div class="grid min-h-[65vh] grid-cols-1 items-center md:grid-cols-2">
			<div
				class="relative z-10 row-span-1 row-start-1 -my-10 aspect-[1/1.3] overflow-hidden md:col-span-1 md:col-start-2 md:mt-0"
			>
				<Scene />
			</div>
			<div class="col-start-1 md:row-start-1">
				<h1
					class="mb-2 md:mb-8 text-[clamp(3rem,20vmin,13rem)] font-extrabold leading-none tracking-tighter text-nowrap"
					aria-label={slice.primary.first_name + ' ' + slice.primary.last_name}
				>
					{#if first_name_letters.length && last_name_letters.length}
						<span class="block text-slate-300">
							{#each first_name_letters as letter}
								<span class="name-animation inline-block opacity-0">{letter}</span>
							{/each}
						</span>
						<span class="block text-slate-500 -mt-[.2em]">
							{#each last_name_letters as letter}
								<span class="name-animation inline-block opacity-0">{letter}</span>
							{/each}
						</span>
					{/if}
				</h1>
				<span
					class="job-title block bg-gradient-to-tr from-yellow-500 via-yellow-200 to-yellow-500 bg-clip-text text-transparent text-2xl font-bold uppercase tracking-[.2em] md:text-4xl opacity-0"
				>
					{slice.primary.tag_line}
				</span>
			</div>
		</div>
	</div>
</section>
