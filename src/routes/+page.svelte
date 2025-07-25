<script>
	import { insults } from '$lib/words.js';

	let currentSentence = 'Baliga-balaga';

	function generateSentence() {
		const insult = insults[Math.floor(Math.random() * insults.length)];

		currentSentence = `${insult}`;
	}
</script>

<main>
	<h1>🎲 Catalan Insults Generator</h1>
	<div class="sentence-display">
		{currentSentence}
	</div>
	<button on:click={generateSentence}>Generate New Sentence</button>
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

	.sentence-display {
		background: #f0f0f0;
		border-radius: 10px;
		padding: 30px;
		margin: 30px 0;
		font-size: 1.2em;
		min-height: 60px;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	button {
		background: #4caf50;
		color: white;
		border: none;
		padding: 15px 30px;
		font-size: 1em;
		border-radius: 5px;
		cursor: pointer;
	}

	button:hover {
		background: #45a049;
	}
</style>
