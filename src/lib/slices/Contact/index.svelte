<script lang="ts">
	import Bounded from '$lib/components/Bounded.svelte';
	import type { Content } from '@prismicio/client';
	import { PrismicRichText, PrismicImage } from '@prismicio/svelte';
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';

	export let slice: Content.ContactSlice;

	let name = '';
	let email = '';
	let message = '';
	let isSubmitting = false;
	let submitStatus = { success: false, message: '' };

	function validateEmail(email: string) {
		return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
	}

	async function handleSubmit() {
		if (!name || !email || !message) {
			submitStatus = { success: false, message: 'Please fill in all fields.' };
			return;
		}

		if (!validateEmail(email)) {
			submitStatus = { success: false, message: 'Please enter a valid email address.' };
			return;
		}

		isSubmitting = true;
		try {
			// Here you would typically send the form data to your backend
			await new Promise(resolve => setTimeout(resolve, 1000)); // Simulated API call
			submitStatus = { success: true, message: 'Thank you for your message! I will get back to you soon.' };
			name = '';
			email = '';
			message = '';
		} catch (error) {
			submitStatus = { success: false, message: 'Something went wrong. Please try again later.' };
		} finally {
			isSubmitting = false;
		}
	}

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
				duration: 1.2,
				ease: 'elastic.out(1, 0.5)'
			}
		);

		tl.fromTo(
			'.form-container',
			{
				opacity: 0,
				y: 30,
				scale: 0.95
			},
			{
				opacity: 1,
				y: 0,
				scale: 1,
				duration: 1,
				ease: 'power3.out'
			}
		);

		// Animate form elements
		tl.fromTo(
			'.form-group',
			{
				opacity: 0,
				y: 20
			},
			{
				opacity: 1,
				y: 0,
				duration: 0.6,
				stagger: 0.15,
				ease: 'power2.out'
			},
			'-=0.4'
		);

		// Add hover animation for inputs
		const inputs = document.querySelectorAll('input, textarea');
		inputs.forEach(input => {
			input.addEventListener('focus', () => {
				gsap.to(input, {
					scale: 1.02,
					duration: 0.3,
					ease: 'power2.out'
				});
			});
			input.addEventListener('blur', () => {
				gsap.to(input, {
					scale: 1,
					duration: 0.3,
					ease: 'power2.out'
				});
			});
		});
	});
</script>

<style>
	.red-glow {
		background: radial-gradient(
			circle at top center,
			rgba(255, 255, 255, 0.15) 0%,
			rgba(255, 255, 255, 0.1) 20%,
			rgba(255, 255, 255, 0.05) 40%,
			rgba(255, 255, 255, 0.02) 60%,
			transparent 80%
		);
		animation: subtle-pulse 4s infinite;
		position: relative;
	}

	.red-glow::after {
		content: '';
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background: inherit;
		filter: blur(30px);
		opacity: 0.5;
		z-index: -1;
	}

	@keyframes subtle-pulse {
		0%, 100% {
			opacity: 0.8;
			transform: scale(1);
		}
		50% {
			opacity: 1;
			transform: scale(1.02);
		}
	}

	@keyframes float {
		0%, 100% {
			transform: translateY(0);
		}
		50% {
			transform: translateY(-10px);
		}
	}

	.form-container {
		max-width: 600px;
		margin: 2rem auto;
		padding: 2.5rem;
		background: rgba(255, 255, 255, 0.08);
		border-radius: 1.5rem;
		box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
		border: 1px solid rgba(255, 255, 255, 0.15);
		backdrop-filter: blur(12px);
		transform-style: preserve-3d;
		transition: transform 0.3s ease, box-shadow 0.3s ease;
	}

	.form-container:hover {
		transform: translateY(-5px);
		box-shadow: 0 12px 40px rgba(0, 0, 0, 0.25);
	}

	.form-group {
		margin-bottom: 1.5rem;
	}

	label {
		display: block;
		margin-bottom: 0.5rem;
		font-size: 0.9rem;
		color: rgba(255, 255, 255, 0.8);
		letter-spacing: 0.5px;
		transform-origin: left;
		transition: transform 0.3s ease, color 0.3s ease;
	}

	input,
	textarea {
		width: 100%;
		padding: 1rem;
		border: 2px solid rgba(255, 255, 255, 0.1);
		border-radius: 0.75rem;
		background: rgba(255, 255, 255, 0.05);
		color: #ffffff;
		font-size: 1rem;
		transition: all 0.3s ease;
	}

	input:focus,
	textarea:focus {
		outline: none;
		border-color: rgba(255, 255, 255, 0.3);
		background: rgba(255, 255, 255, 0.08);
		box-shadow: 0 0 20px rgba(255, 255, 255, 0.1);
	}

	textarea {
		min-height: 120px;
		resize: vertical;
	}

	button {
		width: 100%;
		padding: 1rem;
		background: linear-gradient(135deg, rgba(255, 255, 255, 0.15), rgba(255, 255, 255, 0.05));
		color: white;
		border: 2px solid rgba(255, 255, 255, 0.2);
		border-radius: 0.75rem;
		font-size: 1rem;
		font-weight: 600;
		letter-spacing: 0.5px;
		cursor: pointer;
		transition: all 0.3s ease;
		position: relative;
		overflow: hidden;
	}

	button:hover:not(:disabled) {
		background: linear-gradient(135deg, rgba(255, 255, 255, 0.2), rgba(255, 255, 255, 0.1));
		transform: translateY(-2px);
		box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
	}

	button:disabled {
		background: rgba(255, 255, 255, 0.05);
		border-color: rgba(255, 255, 255, 0.1);
		cursor: not-allowed;
		opacity: 0.7;
	}

	.status-message {
		margin-top: 1.5rem;
		padding: 1rem;
		border-radius: 0.75rem;
		text-align: center;
		font-size: 0.9rem;
		letter-spacing: 0.5px;
		backdrop-filter: blur(8px);
		transition: all 0.3s ease;
	}

	.success {
		background: rgba(39, 174, 96, 0.15);
		border: 1px solid rgba(39, 174, 96, 0.3);
		color: #2ecc71;
	}

	.error {
		background: rgba(231, 76, 60, 0.15);
		border: 1px solid rgba(231, 76, 60, 0.3);
		color: #e74c3c;
	}

	.red-glow {
		background: radial-gradient(
			circle at center,
			rgba(255, 255, 255, 0.2) 0%,
			rgba(255, 255, 255, 0.15) 25%,
			rgba(255, 255, 255, 0.05) 50%,
			transparent 75%
		);
		padding: 2rem;
		border-radius: 1.5rem;
		margin-bottom: 2rem;
		text-align: center;
	}

	.red-glow h1 {
		font-size: 2.5rem;
		font-weight: 700;
		margin: 0;
		background: linear-gradient(135deg, #ffffff 0%, rgba(255, 255, 255, 0.7) 100%);
		-webkit-background-clip: text;
		-webkit-text-fill-color: transparent;
		letter-spacing: 1px;
	}
</style>

<Bounded class="animated" data-slice-type={slice.slice_type} data-slice-variation={slice.variation}>
	<div class="glow-container red-glow">
		<h1>{slice.primary.heading}</h1>
	</div>

	<div class="form-container animated">
		<form on:submit|preventDefault={handleSubmit}>
			<div class="form-group">
				<label for="name">Name</label>
				<input
					type="text"
					id="name"
					bind:value={name}
					placeholder="Your name"
					required
				/>
			</div>

			<div class="form-group">
				<label for="email">Email</label>
				<input
					type="email"
					id="email"
					bind:value={email}
					placeholder="your@email.com"
					required
				/>
			</div>

			<div class="form-group">
				<label for="message">Message</label>
				<textarea
					id="message"
					bind:value={message}
					placeholder="Your message"
					required
				></textarea>
			</div>

			<button type="submit" disabled={isSubmitting}>
				{isSubmitting ? 'Sending...' : 'Send Message'}
			</button>

			{#if submitStatus.message}
				<div class="status-message {submitStatus.success ? 'success' : 'error'}">
					{submitStatus.message}
				</div>
			{/if}
		</form>
	</div>
</Bounded>
