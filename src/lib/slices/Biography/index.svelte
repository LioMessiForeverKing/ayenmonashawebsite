<script lang="ts">
	import Bounded from '$lib/components/Bounded.svelte';
	import Button from '$lib/components/Button.svelte';
	import Heading from '$lib/components/Heading.svelte';
	import type { Content } from '@prismicio/client';
	import { PrismicImage, PrismicRichText } from '@prismicio/svelte';
	import Avatar from './Avatar.svelte';
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';

	export let slice: Content.BiographySlice;

	onMount(() => {
		// Set initial state
		gsap.set(".heading, .description, .button, .avatar", { opacity: 0, y: 20 });

		// Create timeline for animations
		const tl = gsap.timeline({ defaults: { duration: 1, ease: "power2.out" } });

		tl.to(".heading", { y: 0, opacity: 1 })
		  .to(".description", { y: 0, opacity: 1 }, "-=0.5")
		  .to(".button", { y: 0, opacity: 1 }, "-=0.5")
		  .to(".avatar", { y: 0, opacity: 1 }, "-=0.5");
	});
</script>

<Bounded data-slice-type={slice.slice_type} data-slice-variation={slice.variation}>
	<div class="grid gap-x-8 gap-y-6 md:grid-cols-[2fr,1fr]">
		<Heading size="xl" class="col-start-1 heading">
			{slice.primary.heading}
		</Heading>

		<div class="col-start-1 prose prose-invert prose-slate prose-xl description">
			<PrismicRichText field={slice.primary.description} />
		</div>
		<Button linkField={slice.primary.button_link} label={slice.primary.button_label} class="button" />

		<Avatar image={slice.primary.avatar} class="row-start-1 max-w-sm md:col-start-2 md:row-end-3 avatar" />
	</div>
</Bounded>
