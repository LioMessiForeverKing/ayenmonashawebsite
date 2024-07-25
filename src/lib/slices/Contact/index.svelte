<script lang="ts">
	import Bounded from '$lib/components/Bounded.svelte';
	import type { Content } from '@prismicio/client';
	import { PrismicRichText, PrismicImage } from '@prismicio/svelte';
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import '/Users/ayenmonasha/Desktop/ayenwebsite/src/app.css';

	export let slice: Content.ContactSlice;

	onMount(() => {
		const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
		if (prefersReducedMotion) {
			gsap.to('.animated', { opacity: 1 });
			return;
		}

		const tl = gsap.timeline();

		tl.fromTo(
			'.glow-container',
			{
				opacity: 0,
				scale: 0.8,
				y: 50
			},
			{
				opacity: 1,
				scale: 1,
				y: 0,
				duration: 1,
				ease: 'bounce.out'
			}
		);

		tl.fromTo(
			'.description',
			{
				opacity: 0,
				x: -50
			},
			{
				opacity: 1,
				x: 0,
				duration: 1,
				delay: 0.5
			}
		);

		tl.fromTo(
			'.image-wrapper',
			{
				opacity: 0,
				scale: 0.8
			},
			{
				opacity: 1,
				scale: 1,
				duration: 1,
				delay: 0.5
			}
		);
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

	h1 {
		text-align: center;
		font-size: 2rem;
		color: white;
		font-family: 'Urbanist', sans-serif;
		font-weight: 700;
		animation: glowPulse 3s infinite;
		text-shadow: 0 0 10px rgba(255, 255, 255, 0.7);
	}

	@keyframes glowPulse {
		0%, 100% {
			text-shadow: 0 0 10px rgba(255, 255, 255, 0.7);
		}
		50% {
			text-shadow: 0 0 20px rgba(255, 255, 255, 0.9), 0 0 30px rgba(0, 0, 255, 0.8);
		}
	}

	@media (min-width: 640px) {
		h1 {
			font-size: 3rem;
		}
	}

	@media (min-width: 1024px) {
		h1 {
			font-size: 4rem;
		}
	}

	.description {
		text-align: center;
		font-size: 1rem;
		margin-top: 1rem;
		font-family: 'Urbanist', sans-serif;
		font-weight: 400;
	}

	@media (min-width: 640px) {
		.description {
			font-size: 1.25rem;
		}
	}

	@media (min-width: 1024px) {
		.description {
			font-size: 1.5rem;
		}
	}

	.image-wrapper {
		position: relative;
		display: block;
		margin: 2rem auto;
		width: 50%;
	}

	.image-wrapper::before {
		content: '';
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		width: 100%;
		height: 100%;
		background: radial-gradient(circle, rgba(255, 165, 0, 0.7) 0%, rgba(0, 0, 0, 0) 100%);
		animation: expandGlow 4s infinite ease-out;
		z-index: -1;
	}

	@keyframes expandGlow {
		0% {
			width: 100%;
			height: 100%;
			opacity: 0.7;
		}
		100% {
			width: 200%;
			height: 200%;
			opacity: 0;
		}
	}

	@media (min-width: 1024px) {
		.image-wrapper {
			width: 30%;
		}
	}
</style>

<Bounded class="animated" data-slice-type={slice.slice_type} data-slice-variation={slice.variation}>
	<div class="glow-container red-glow">
		<h1>{slice.primary.heading}</h1>
	</div>
	<div class="description animated">
		<PrismicRichText field={slice.primary.description} />
	</div>
	<div class="image-wrapper animated">
		<PrismicImage field={slice.primary.image} />
	</div>
</Bounded>
