<script lang="ts">
	import Bounded from '$lib/components/Bounded.svelte';
	import Heading from '$lib/components/Heading.svelte';
	import type { Content } from '@prismicio/client';
	import { PrismicRichText } from '@prismicio/svelte';
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';

	export let slice: Content.ExperienceSlice;

	onMount(() => {
		const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
		if (prefersReducedMotion) {
			gsap.to('.experience-item', { opacity: 1 });
			return;
		}

		gsap.fromTo(
			'.experience-item',
			{
				opacity: 0,
				y: 50,
				scale: 0.9
			},
			{
				opacity: 1,
				y: 0,
				scale: 1,
				stagger: 0.2,
				duration: 0.8,
				ease: 'power3.out'
			}
		);
	});
</script>

<style>
	.experience-container {
		position: relative;
		padding: 2rem 0;
	}

	.experience-item {
		position: relative;
		background: rgba(255, 255, 255, 0.05);
		border: 1px solid rgba(255, 255, 255, 0.1);
		border-radius: 1rem;
		padding: 2rem;
		margin-bottom: 2rem;
		transition: all 0.3s ease;
		backdrop-filter: blur(10px);
		box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
	}

	.experience-item::before {
		content: '';
		position: absolute;
		left: -2rem;
		top: 50%;
		width: 1rem;
		height: 1rem;
		background: rgba(255, 255, 255, 0.1);
		border: 2px solid rgba(255, 255, 255, 0.2);
		border-radius: 50%;
		transform: translateY(-50%);
	}

	.experience-item::after {
		content: '';
		position: absolute;
		left: -1.6rem;
		top: 50%;
		width: 2px;
		height: calc(100% + 2rem);
		background: linear-gradient(to bottom, rgba(255, 255, 255, 0.2), transparent);
		transform: translateY(-50%);
		z-index: -1;
	}

	.experience-item:last-child::after {
		display: none;
	}

	.experience-item:hover {
		transform: translateY(-5px);
		box-shadow: 0 8px 12px rgba(0, 0, 0, 0.2);
		border-color: rgba(255, 255, 255, 0.2);
		background: rgba(255, 255, 255, 0.08);
	}

	.title {
		color: #ffffff;
		font-size: 1.5rem;
		font-weight: 600;
		margin-bottom: 0.5rem;
	}

	.meta {
		display: flex;
		align-items: center;
		gap: 0.5rem;
		color: rgba(255, 255, 255, 0.7);
		font-size: 1.1rem;
		margin-bottom: 1rem;
	}

	.separator {
		color: rgba(255, 255, 255, 0.3);
	}

	.description {
		color: rgba(255, 255, 255, 0.9);
		line-height: 1.6;
	}

	@media (max-width: 768px) {
		.experience-item {
			margin-left: 2rem;
		}
	}
</style>

<Bounded data-slice-type={slice.slice_type} data-slice-variation={slice.variation}>
	<div class="experience-container">
		<Heading tag="h2" size="lg" class="mb-12">
			{slice.primary.heading}
		</Heading>
		{#each slice.primary.item as item}
			<div class="experience-item">
				<h3 class="title">{item.title}</h3>
				<div class="meta">
					<span>{item.time_period}</span>
					<span class="separator">•</span>
					<span>{item.institution}</span>
				</div>
				<div class="description prose prose-lg prose-invert">
					<PrismicRichText field={item.description} />
				</div>
			</div>
		{/each}
	</div>
</Bounded>