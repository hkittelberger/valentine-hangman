<script>
	let targetPhrase = "this is a test";
	let guessedLetters = [];
	let wrongGuesses = 0;
	let maxWrongGuesses = 6;
	
	// All 26 letters of the alphabet
	const alphabet = 'abcdefghijklmnopqrstuvwxyz'.split('');
	
	// Split phrase into words and handle display
	$: words = targetPhrase.toLowerCase().split(' ');
	$: displayWords = words.map(word => 
		word.split('').map(letter => 
			guessedLetters.includes(letter) ? letter.toUpperCase() : '_'
		).join(' ')
	);
	
	// Check if letter has been guessed
	$: isLetterGuessed = (letter) => guessedLetters.includes(letter);
	
	// Check if letter is in the target phrase
	$: isCorrectLetter = (letter) => targetPhrase.toLowerCase().includes(letter);
	
	// Check win condition
	$: hasWon = words.every(word => 
		word.split('').every(letter => guessedLetters.includes(letter))
	);
	
	// Check lose condition
	$: hasLost = wrongGuesses >= maxWrongGuesses;
	
	// Game over condition
	$: gameOver = hasWon || hasLost;
	
	function guessLetter(letter) {
		if (isLetterGuessed(letter) || gameOver) return;
		
		guessedLetters = [...guessedLetters, letter];
		
		if (!isCorrectLetter(letter)) {
			wrongGuesses++;
		}
	}
	
	function resetGame() {
		guessedLetters = [];
		wrongGuesses = 0;
	}
</script>

<div class="min-h-screen bg-gray-100 flex flex-col items-center justify-center p-8">
	<div class="max-w-4xl w-full bg-white rounded-lg shadow-lg p-8">
		<h1 class="text-4xl font-bold text-center mb-8 text-gray-800">Hangman Game</h1>
		
		<!-- Wrong guesses counter -->
		<div class="text-center mb-6">
			<p class="text-lg">Wrong guesses: {wrongGuesses} / {maxWrongGuesses}</p>
		</div>
		
		<!-- Word display -->
		<div class="text-center mb-8">
			{#each displayWords as word, i}
				<div class="inline-block mx-4">
					<span class="text-3xl font-mono tracking-widest">{word}</span>
				</div>
			{/each}
		</div>
		
		<!-- Game status -->
		{#if hasWon}
			<div class="text-center mb-6">
				<p class="text-2xl text-green-600 font-bold">🎉 You Won! 🎉</p>
			</div>
		{:else if hasLost}
			<div class="text-center mb-6">
				<p class="text-2xl text-red-600 font-bold">💀 Game Over! 💀</p>
				<p class="text-lg mt-2">The answer was: <span class="font-bold uppercase">{targetPhrase}</span></p>
			</div>
		{/if}
		
		<!-- Reset button -->
		{#if gameOver}
			<div class="text-center mb-6">
				<button 
					on:click={resetGame}
					class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
				>
					Play Again
				</button>
			</div>
		{/if}
		
		<!-- Alphabet buttons in 3 rows -->
		<div class="flex flex-col items-center space-y-2">
			<!-- First row: Q-P (10 letters) -->
			<div class="flex flex-wrap justify-center gap-2">
				{#each alphabet.slice(0, 10) as letter}
					<button
						on:click={() => guessLetter(letter)}
						disabled={isLetterGuessed(letter) || gameOver}
						class="w-10 h-10 rounded border-2 font-bold text-lg transition-colors
							{isLetterGuessed(letter) 
								? isCorrectLetter(letter) 
									? 'bg-green-500 text-white border-green-500' 
									: 'bg-red-500 text-white border-red-500'
								: gameOver
									? 'bg-gray-300 text-gray-500 border-gray-300 cursor-not-allowed'
									: 'bg-white hover:bg-gray-100 border-gray-300 hover:border-gray-400 cursor-pointer'
							}"
					>
						{letter.toUpperCase()}
					</button>
				{/each}
			</div>
			
			<!-- Second row: A-L (9 letters) -->
			<div class="flex flex-wrap justify-center gap-2">
				{#each alphabet.slice(10, 19) as letter}
					<button
						on:click={() => guessLetter(letter)}
						disabled={isLetterGuessed(letter) || gameOver}
						class="w-10 h-10 rounded border-2 font-bold text-lg transition-colors
							{isLetterGuessed(letter) 
								? isCorrectLetter(letter) 
									? 'bg-green-500 text-white border-green-500' 
									: 'bg-red-500 text-white border-red-500'
								: gameOver
									? 'bg-gray-300 text-gray-500 border-gray-300 cursor-not-allowed'
									: 'bg-white hover:bg-gray-100 border-gray-300 hover:border-gray-400 cursor-pointer'
							}"
					>
						{letter.toUpperCase()}
					</button>
				{/each}
			</div>
			
			<!-- Third row: Z-M (7 letters) -->
			<div class="flex flex-wrap justify-center gap-2">
				{#each alphabet.slice(19, 26) as letter}
					<button
						on:click={() => guessLetter(letter)}
						disabled={isLetterGuessed(letter) || gameOver}
						class="w-10 h-10 rounded border-2 font-bold text-lg transition-colors
							{isLetterGuessed(letter) 
								? isCorrectLetter(letter) 
									? 'bg-green-500 text-white border-green-500' 
									: 'bg-red-500 text-white border-red-500'
								: gameOver
									? 'bg-gray-300 text-gray-500 border-gray-300 cursor-not-allowed'
									: 'bg-white hover:bg-gray-100 border-gray-300 hover:border-gray-400 cursor-pointer'
							}"
					>
						{letter.toUpperCase()}
					</button>
				{/each}
			</div>
		</div>
	</div>
</div>
