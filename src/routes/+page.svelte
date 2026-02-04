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

<div class="max-w-2xl mx-auto p-8 text-center font-sans">
	<h1 class="text-pink-500 text-4xl font-bold mb-8">Valentine's Hangman</h1>
	
	<div class="mb-6">
		<p class="text-lg">Wrong guesses: {wrongGuesses} / {maxWrongGuesses}</p>
	</div>
	
	<div class="text-4xl font-bold my-8 tracking-widest text-gray-800 flex justify-center gap-4 flex-wrap">
		{#each displayWords as word, i}
			<span class="font-mono">{word}</span>
		{/each}
	</div>
	
	{#if gameWon}
		<div class="text-2xl font-bold my-6 p-4 rounded-lg bg-green-100 text-green-800 border-2 border-green-200">🎉 You won! 🎉</div>
	{:else if gameLost}
		<div class="text-2xl font-bold my-6 p-4 rounded-lg bg-red-100 text-red-800 border-2 border-red-200">💀 Game over! The phrase was: "{targetPhrase}" 💀</div>
	{/if}
	
	{#if gameOver}
		<button 
			class="bg-pink-500 hover:bg-pink-600 text-white font-bold py-3 px-6 rounded-md text-lg transition-colors mb-8"
			on:click={resetGame}
		>
			Play Again
		</button>
	{/if}
	
	<div class="mt-8">
		{#each letterRows as row}
			<div class="flex justify-center gap-2 mb-2">
				{#each row as letter}
					<button 
						class="w-12 h-12 border-2 border-gray-300 bg-white text-lg font-bold rounded-md cursor-pointer transition-all duration-200 hover:bg-gray-100 hover:border-pink-500 disabled:cursor-not-allowed
						{guessedLetters.has(letter) && targetPhrase.toLowerCase().includes(letter) ? 'bg-green-100 border-green-500 text-green-800' : ''}
						{guessedLetters.has(letter) && !targetPhrase.toLowerCase().includes(letter) ? 'bg-red-100 border-red-500 text-red-800' : ''}"
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


