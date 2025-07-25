<script>
	import { browser } from '$app/environment';
	import { onMount } from 'svelte';
	import { insults } from '$lib/words.js';
	import confetti from 'canvas-confetti';

	let currentSentence = 'Baliga-balaga';
	let isSpinning = false;
	let wordsQueue = [];

	let voicesLoading = true;
	let voices = [];
	let selectedVoiceIndex = 0;

	onMount(() => {
		if (browser && typeof speechSynthesis !== 'undefined') {
			const loadVoices = () => {
				voicesLoading = false;
				voices = speechSynthesis.getVoices();

				const catalanVoiceIndex = voices.findIndex(
					(voice) => voice.lang.startsWith('ca') || voice.lang.includes('cat')
				);
				if (catalanVoiceIndex !== -1) {
					selectedVoiceIndex = catalanVoiceIndex;
				}
			};

			if (speechSynthesis.getVoices().length > 0) {
				loadVoices();
			} else {
				speechSynthesis.addEventListener('voiceschanged', loadVoices);
			}
		} else {
			voicesLoading = false;
		}
	});

	$: selectedVoice = voices[selectedVoiceIndex];

	function speakText(text) {
		if (!browser || typeof speechSynthesis === 'undefined' || voices.length === 0) return;

		speechSynthesis.cancel();

		const utterance = new SpeechSynthesisUtterance(text);
		if (selectedVoice) {
			utterance.voice = selectedVoice;
		}

		utterance.rate = 0.9;
		utterance.pitch = 1.1;

		speechSynthesis.speak(utterance);
	}

	function generateSentence() {
		if (isSpinning) return;

		isSpinning = true;
		wordsQueue = [];

		const totalWords = 8;
		for (let i = 0; i < totalWords; i++) {
			wordsQueue.push(insults[Math.floor(Math.random() * insults.length)]);
		}
		wordsQueue.push(insults[Math.floor(Math.random() * insults.length)]);

		let currentIndex = 0;
		let speed = 10;

		const slideNext = () => {
			if (currentIndex < wordsQueue.length) {
				currentSentence = wordsQueue[currentIndex];
				currentIndex++;

				if (currentIndex > totalWords - 2) {
					speed += 40;
				} else if (currentIndex > totalWords - 4) {
					speed += 20;
				} else if (currentIndex > 2) {
					speed += 10;
				}

				setTimeout(slideNext, speed);
			} else {
				isSpinning = false;
				confetti({
					particleCount: 100,
					spread: 70,
					origin: { y: 0.6 }
				});

				setTimeout(() => {
					speakText(currentSentence);
				}, 500);
			}
		};

		slideNext();
	}
</script>

<main>
	<h1>🎰 Catalan Insults Generator</h1>

	<div class="slot-container">
		<div class="sentence-display" class:spinning={isSpinning}>
			<div class="word-slide" class:sliding={isSpinning}>
				{currentSentence}
			</div>
		</div>
	</div>

	<div class="button-container">
		<button on:click={generateSentence} disabled={isSpinning}>
			{isSpinning ? '🎰 Carregant...' : "Insulta'm"}
		</button>

		<button
			on:click={() => speakText(currentSentence)}
			disabled={!browser ||
				typeof speechSynthesis === 'undefined' ||
				voicesLoading ||
				isSpinning ||
				voices.length === 0}
			class="speak-button"
		>
			🔊 Repeteix
		</button>
	</div>

	{#if !browser || typeof speechSynthesis === 'undefined'}
		<div class="voice-loading">Opció de text a veu no disponible</div>
	{:else if voicesLoading}
		<div class="voice-loading">Carregant veus...</div>
	{:else if voices.length === 0}
		<div class="voice-loading">No s'han trobat veus disponibles</div>
	{:else}
		<div class="voice-selector">
			<label for="voice-select">Selecciona la veu:</label>
			<select id="voice-select" bind:value={selectedVoiceIndex}>
				{#each voices as voice, index}
					<option value={index}>
						{voice.name} ({voice.lang})
					</option>
				{/each}
			</select>
		</div>
	{/if}
</main>

<style>
	main {
		max-width: 600px;
		margin: 50px auto;
		padding: 20px;
		text-align: center;
		font-family: 'Segoe UI', sans-serif;
	}

	h1 {
		color: #333;
		margin-bottom: 30px;
	}

	.slot-container {
		perspective: 1000px;
	}

	.sentence-display {
		background: #f0f0f0;
		border-radius: 10px;
		padding: 30px;
		margin: 30px 0;
		font-size: 1.5em;
		min-height: 80px;
		display: flex;
		align-items: center;
		justify-content: center;
		border: 3px solid #e2e8f0;
		position: relative;
		overflow: hidden;
		font-weight: bold;
	}

	.sentence-display.spinning {
		background: #fff3cd;
		border-color: #ffc107;
		box-shadow: 0 0 20px rgba(255, 193, 7, 0.3);
	}

	.word-slide {
		transition: transform 0.08s ease-out;
		transform: translateY(0);
	}

	.word-slide.sliding {
		animation: slideDown 0.08s ease-out infinite;
	}

	@keyframes slideDown {
		0% {
			transform: translateY(-30px);
			opacity: 0.7;
		}
		50% {
			transform: translateY(0);
			opacity: 1;
		}
		100% {
			transform: translateY(30px);
			opacity: 0.7;
		}
	}

	.button-container {
		display: flex;
		gap: 10px;
		justify-content: center;
		flex-wrap: wrap;
		margin-bottom: 20px;
	}

	button {
		background: linear-gradient(45deg, #ff6b6b, #ee5a52);
		color: white;
		border: none;
		padding: 15px 30px;
		font-size: 1.1em;
		border-radius: 8px;
		cursor: pointer;
		min-width: 150px;
		transition: all 0.2s ease;
		font-weight: bold;
		text-transform: uppercase;
	}

	.speak-button {
		background: linear-gradient(45deg, #4caf50, #45a049);
		min-width: 130px;
	}

	.speak-button:hover:not(:disabled) {
		background: linear-gradient(45deg, #45a049, #3d8b40);
	}

	button:hover:not(:disabled) {
		background: linear-gradient(45deg, #ee5a52, #dc4545);
		transform: translateY(-2px);
		box-shadow: 0 5px 15px rgba(238, 90, 82, 0.3);
	}

	button:disabled {
		background: #cccccc;
		cursor: not-allowed;
		transform: none;
		box-shadow: none;
	}

	.voice-loading {
		color: #666;
		font-style: italic;
		margin-top: 20px;
	}

	.voice-selector {
		margin-top: 20px;
		padding: 15px;
		background: #f8f9fa;
		border-radius: 8px;
		border: 1px solid #e9ecef;
	}

	.voice-selector label {
		display: block;
		margin-bottom: 8px;
		font-weight: bold;
		color: #333;
	}

	select {
		width: 100%;
		max-width: 400px;
		padding: 10px;
		font-size: 1em;
		border: 2px solid #ddd;
		border-radius: 6px;
		background: white;
		cursor: pointer;
		transition: border-color 0.2s ease;
	}

	select:focus {
		outline: none;
		border-color: #ff6b6b;
		box-shadow: 0 0 5px rgba(255, 107, 107, 0.3);
	}

	.sentence-display::before {
		content: '';
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		height: 2px;
		background: linear-gradient(90deg, transparent, #666, transparent);
		opacity: 0.3;
	}

	.sentence-display::after {
		content: '';
		position: absolute;
		bottom: 0;
		left: 0;
		right: 0;
		height: 2px;
		background: linear-gradient(90deg, transparent, #666, transparent);
		opacity: 0.3;
	}
</style>
