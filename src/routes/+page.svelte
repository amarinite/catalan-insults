<script>
	import { insults } from '$lib/words.js';
	import confetti from 'canvas-confetti';

	let currentSentence = 'Baliga-balaga';
	let isSpinning = false;
	let wordsQueue = [];
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
	</div>
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
