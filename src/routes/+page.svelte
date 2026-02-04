<script>
	let targetPhrase = "this is a test";
	let guessedLetters = new Set();
	let wrongGuesses = 0;
	let maxWrongGuesses = 6;
	
	const alphabet = 'abcdefghijklmnopqrstuvwxyz'.split('');
	const letterRows = [
		alphabet.slice(0, 9),   // a-i
		alphabet.slice(9, 17),  // j-q
		alphabet.slice(17, 26)  // r-z
	];
	
	function guessLetter(letter) {
		if (guessedLetters.has(letter)) return;
		
		guessedLetters.add(letter);
		guessedLetters = guessedLetters; // trigger reactivity
		
		if (!targetPhrase.toLowerCase().includes(letter)) {
			wrongGuesses++;
		}
	}
	
	function getDisplayWord() {
		return targetPhrase
			.split(' ')
			.map(word => 
				word.split('').map(char => 
					guessedLetters.has(char.toLowerCase()) ? char : '_'
				).join('')
			);
	}
	
	function isGameWon() {
		const letters = targetPhrase.toLowerCase().replace(/\s/g, '');
		return letters.split('').every(letter => guessedLetters.has(letter));
	}
	
	function isGameLost() {
		return wrongGuesses >= maxWrongGuesses;
	}
	
	function resetGame() {
		guessedLetters = new Set();
		wrongGuesses = 0;
	}
	
	$: displayWords = getDisplayWord();
	$: gameWon = isGameWon();
	$: gameLost = isGameLost();
	$: gameOver = gameWon || gameLost;
</script>

<div class="game-container">
	<h1>Valentine's Hangman</h1>
	
	<div class="game-info">
		<p>Wrong guesses: {wrongGuesses} / {maxWrongGuesses}</p>
	</div>
	
	<div class="word-display">
		{#each displayWords as word, i}
			<span class="word">{word}</span>
		{/each}
	</div>
	
	{#if gameWon}
		<div class="game-message win">🎉 You won! 🎉</div>
	{:else if gameLost}
		<div class="game-message lose">💀 Game over! The phrase was: "{targetPhrase}" 💀</div>
	{/if}
	
	{#if gameOver}
		<button class="reset-btn" on:click={resetGame}>Play Again</button>
	{/if}
	
	<div class="alphabet">
		{#each letterRows as row}
			<div class="letter-row">
				{#each row as letter}
					<button 
						class="letter-btn"
						class:guessed={guessedLetters.has(letter)}
						class:correct={guessedLetters.has(letter) && targetPhrase.toLowerCase().includes(letter)}
						class:wrong={guessedLetters.has(letter) && !targetPhrase.toLowerCase().includes(letter)}
						disabled={guessedLetters.has(letter) || gameOver}
						on:click={() => guessLetter(letter)}
					>
						{letter.toUpperCase()}
					</button>
				{/each}
			</div>
		{/each}
	</div>
</div>

<style>
	.game-container {
		max-width: 600px;
		margin: 0 auto;
		padding: 2rem;
		text-align: center;
		font-family: Arial, sans-serif;
	}
	
	h1 {
		color: #ff6b9d;
		margin-bottom: 2rem;
	}
	
	.game-info {
		margin-bottom: 1.5rem;
		font-size: 1.1rem;
	}
	
	.word-display {
		font-size: 2.5rem;
		font-weight: bold;
		margin: 2rem 0;
		letter-spacing: 0.2em;
		color: #333;
		display: flex;
		justify-content: center;
		gap: 1rem;
		flex-wrap: wrap;
	}
	
	.word {
		font-family: 'Courier New', monospace;
	}
	
	.game-message {
		font-size: 1.5rem;
		font-weight: bold;
		margin: 1.5rem 0;
		padding: 1rem;
		border-radius: 8px;
	}
	
	.win {
		background-color: #d4edda;
		color: #155724;
		border: 2px solid #c3e6cb;
	}
	
	.lose {
		background-color: #f8d7da;
		color: #721c24;
		border: 2px solid #f5c6cb;
	}
	
	.reset-btn {
		background-color: #ff6b9d;
		color: white;
		border: none;
		padding: 0.75rem 1.5rem;
		font-size: 1.1rem;
		border-radius: 6px;
		cursor: pointer;
		margin-bottom: 2rem;
		transition: background-color 0.2s;
	}
	
	.reset-btn:hover {
		background-color: #e55a89;
	}
	
	.alphabet {
		margin-top: 2rem;
	}
	
	.letter-row {
		display: flex;
		justify-content: center;
		gap: 0.5rem;
		margin-bottom: 0.5rem;
	}
	
	.letter-btn {
		width: 50px;
		height: 50px;
		border: 2px solid #ddd;
		background-color: white;
		font-size: 1.2rem;
		font-weight: bold;
		border-radius: 6px;
		cursor: pointer;
		transition: all 0.2s;
	}
	
	.letter-btn:hover:not(:disabled) {
		background-color: #f0f0f0;
		border-color: #ff6b9d;
	}
	
	.letter-btn:disabled {
		cursor: not-allowed;
	}
	
	.letter-btn.correct {
		background-color: #d4edda;
		border-color: #28a745;
		color: #155724;
	}
	
	.letter-btn.wrong {
		background-color: #f8d7da;
		border-color: #dc3545;
		color: #721c24;
	}
	
	@media (max-width: 480px) {
		.word-display {
			font-size: 2rem;
		}
		
		.letter-btn {
			width: 40px;
			height: 40px;
			font-size: 1rem;
		}
		
		.letter-row {
			gap: 0.3rem;
		}
	}
</style>
