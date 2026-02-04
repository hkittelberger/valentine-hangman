<script>
	let targetPhrase = "amelia, will you be my valentine?";
	let guessedLetters = [];
	let wrongGuesses = 0;
	let maxWrongGuesses = 5;
	
	// All 26 letters of the alphabet
	const alphabet = 'abcdefghijklmnopqrstuvwxyz'.split('');
	
	// Split phrase into words and handle display
	$: words = targetPhrase.toLowerCase().split(' ');
	$: displayWords = words.map(word => 
		word.split('').map(letter => {
			// If it's not a letter (punctuation, numbers, etc.), show it
			if (!/[a-z]/.test(letter)) {
				return letter.toUpperCase();
			}
			// If it's a letter, check if it's been guessed
			return guessedLetters.includes(letter) ? letter.toUpperCase() : '_';
		}).join(' ')
	);
	
	// Check if letter has been guessed
	$: isLetterGuessed = (letter) => guessedLetters.includes(letter);
	
	// Check if letter is in the target phrase
	$: isCorrectLetter = (letter) => targetPhrase.toLowerCase().includes(letter);
	
	// Check win condition
	$: hasWon = words.every(word => 
		word.split('').every(letter => {
			// Non-letters don't need to be guessed
			if (!/[a-z]/.test(letter)) return true;
			// Letters need to be guessed
			return guessedLetters.includes(letter);
		})
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

<div class="min-h-screen bg-gradient-to-br from-pink-200 via-red-100 to-rose-200 flex flex-col items-center justify-center p-8">
	<div class="max-w-4xl w-full bg-white rounded-3xl shadow-2xl p-8 border-4 border-pink-300">
		
		<!-- Title -->
		<div class="text-center mb-8">
			<h1 class="text-6xl font-bold text-transparent bg-gradient-to-r from-pink-500 via-red-500 to-pink-600 bg-clip-text mb-2 leading-tight py-2" style="font-family: 'Brush Script MT', cursive;">
				Hangman
			</h1>
		</div>
		
		<!-- Hearts display for wrong guesses -->
		<div class="text-center mb-6">
			<div class="flex justify-center space-x-2">
				{#each Array(maxWrongGuesses) as _, i}
					<span class="text-4xl transition-all duration-300 {i < (maxWrongGuesses - wrongGuesses) ? 'opacity-100 scale-100' : 'opacity-30 scale-75'}">
						❤️
					</span>
				{/each}
			</div>
		</div>
		
		<!-- Word display -->
		<div class="text-center mb-8 bg-gradient-to-r from-pink-50 to-red-50 p-6 rounded-2xl border-2 border-pink-200">
			{#each displayWords as word, i}
				<div class="block mb-2">
					<span class="text-4xl font-mono tracking-widest text-red-600 font-bold">{word}</span>
				</div>
			{/each}
		</div>
		
		<!-- Game status -->
		{#if hasWon}
			<div class="text-center mb-6">
				<p class="text-3xl text-pink-500 font-bold">💖 You Won! Love is in the air! 💖</p>
			</div>
		{:else if hasLost}
			<div class="text-center mb-6">
				<p class="text-3xl text-red-500 font-bold">� Heartbreak! Game Over! �</p>
				<p class="text-lg mt-2 text-pink-700">The answer was: <span class="font-bold uppercase text-red-600">{targetPhrase}</span></p>
			</div>
		{/if}
		
		<!-- Reset button -->
		{#if gameOver}
			<div class="text-center mb-6">
				<button 
					on:click={resetGame}
					class="bg-gradient-to-r from-pink-400 to-red-400 hover:from-pink-500 hover:to-red-500 text-white font-bold py-3 px-6 rounded-full shadow-lg transform transition hover:scale-105"
				>
					💕 Play Again 💕
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
						class="w-12 h-12 rounded-full border-3 font-bold text-lg transition-all duration-200 shadow-md
							{isLetterGuessed(letter) 
								? isCorrectLetter(letter) 
									? 'bg-gradient-to-r from-pink-400 to-red-400 text-white border-pink-500 shadow-lg' 
									: 'bg-gradient-to-r from-gray-400 to-gray-500 text-white border-gray-500'
								: gameOver
									? 'bg-gray-200 text-gray-400 border-gray-300 cursor-not-allowed'
									: 'bg-gradient-to-r from-pink-100 to-red-100 hover:from-pink-200 hover:to-red-200 border-pink-300 hover:border-pink-400 cursor-pointer text-pink-700 hover:shadow-lg transform hover:scale-110'
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
						class="w-12 h-12 rounded-full border-3 font-bold text-lg transition-all duration-200 shadow-md
							{isLetterGuessed(letter) 
								? isCorrectLetter(letter) 
									? 'bg-gradient-to-r from-pink-400 to-red-400 text-white border-pink-500 shadow-lg' 
									: 'bg-gradient-to-r from-gray-400 to-gray-500 text-white border-gray-500'
								: gameOver
									? 'bg-gray-200 text-gray-400 border-gray-300 cursor-not-allowed'
									: 'bg-gradient-to-r from-pink-100 to-red-100 hover:from-pink-200 hover:to-red-200 border-pink-300 hover:border-pink-400 cursor-pointer text-pink-700 hover:shadow-lg transform hover:scale-110'
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
						class="w-12 h-12 rounded-full border-3 font-bold text-lg transition-all duration-200 shadow-md
							{isLetterGuessed(letter) 
								? isCorrectLetter(letter) 
									? 'bg-gradient-to-r from-pink-400 to-red-400 text-white border-pink-500 shadow-lg' 
									: 'bg-gradient-to-r from-gray-400 to-gray-500 text-white border-gray-500'
								: gameOver
									? 'bg-gray-200 text-gray-400 border-gray-300 cursor-not-allowed'
									: 'bg-gradient-to-r from-pink-100 to-red-100 hover:from-pink-200 hover:to-red-200 border-pink-300 hover:border-pink-400 cursor-pointer text-pink-700 hover:shadow-lg transform hover:scale-110'
							}"
					>
						{letter.toUpperCase()}
					</button>
				{/each}
			</div>
		</div>
	</div>
</div>
