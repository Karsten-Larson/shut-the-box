<script lang="ts">
	import { flip } from 'svelte/animate';
	import { quintOut } from 'svelte/easing';
	import { fade } from 'svelte/transition';
	import Confetti from 'svelte-confetti';
	import Modal from '$lib/Modal.svelte';

	let numbers = Array.from({ length: 12 }, (_, i) => ({
		value: i + 1,
		active: true
	}));
	let dice = [1, 1];
	let diceRolled = false;
	let selectedNumbers: number[] = [];
	let score = 0;
	let gameOver = false;
	let rolling = false;
	let showConfetti = false;
	let showModal = false;
	let showInvalidSelectionTooltip = false;
	let strikes = 0;
	const maxStrikes = 3;
	let modalTitle = '';
	let modalMessage = '';

	function rollDiceWithAnimation() {
		rolling = true;
		const interval = setInterval(() => {
			dice = [Math.floor(Math.random() * 6) + 1, Math.floor(Math.random() * 6) + 1];
		}, 50);

		setTimeout(() => {
			clearInterval(interval);
			rollDice();
			rolling = false;
		}, 1000);
	}

	function newGame() {
		numbers = Array.from({ length: 12 }, (_, i) => ({
			value: i + 1,
			active: true
		}));
		dice = [1, 1];
		diceRolled = false;
		selectedNumbers = [];
		score = 0;
		gameOver = false;
		showConfetti = false;
		showModal = false;
		showInvalidSelectionTooltip = false;
		strikes = 0;
	}

	function rollDice() {
		dice = [Math.floor(Math.random() * 6) + 1, Math.floor(Math.random() * 6) + 1];
		diceRolled = true;
		selectedNumbers = [];

		const openNumbers = numbers.filter((n) => n.active).map((n) => n.value);
		const diceSum = dice[0] + dice[1];

		let canMakeMove = false;
		for (let i = 1; i < 1 << openNumbers.length; i++) {
			let subset = [];
			for (let j = 0; j < openNumbers.length; j++) {
				if ((i >> j) & 1) {
					subset.push(openNumbers[j]);
				}
			}
			if (subset.reduce((a, b) => a + b, 0) === diceSum) {
				canMakeMove = true;
				break;
			}
		}

		if (!canMakeMove) {
			strikes++;
			if (strikes >= maxStrikes) {
				score = openNumbers.reduce((a, b) => a + b, 0);
				gameOver = true;
				showGameOverModal();
			} else {
				diceRolled = false;
			}
		}
	}

	function toggleNumber(value: number) {
		if (!diceRolled || gameOver) return;

		if (selectedNumbers.includes(value)) {
			selectedNumbers = selectedNumbers.filter((n) => n !== value);
		} else {
			selectedNumbers = [...selectedNumbers, value];
		}
	}

	function shutTheBox() {
		const selectedSum = selectedNumbers.reduce((a, b) => a + b, 0);
		const diceSum = dice[0] + dice[1];

		if (selectedSum === diceSum) {
			numbers = numbers.map((n) => {
				if (selectedNumbers.includes(n.value)) {
					return { ...n, active: false };
				}
				return n;
			});
			diceRolled = false;
			selectedNumbers = [];

			if (numbers.every((n) => !n.active)) {
				gameOver = true;
				score = 0;
				showWinModal();
			}
		} else {
			showInvalidSelectionTooltip = true;
			setTimeout(() => {
				showInvalidSelectionTooltip = false;
			}, 3000);
		}
	}

	function showWinModal() {
		modalTitle = 'You Win!';
		let message = 'Congratulations, you shut the box!';
		if (strikes > 0) {
			message += `<br><span class="text-lg text-gray-400">You used ${strikes} strike(s).</span>`;
		}
		modalMessage = message;
		showConfetti = true;
		setTimeout(() => {
			showConfetti = false;
			showModal = true;
		}, 3000);
	}

	function showGameOverModal() {
		modalTitle = 'Game Over!';
		modalMessage = `Your score is ${score}`;
		showModal = true;
	}

	const quirkyLines = [
		'Did you know? Mathematicians secretly love this game.',
		'Warning: May cause sudden outbursts of joy or despair.',
		'Rolling double sixes is not a personality trait.',
		'Shutting the box is more satisfying than closing a browser tab.',
		'Legend says the box never truly shuts for sore losers.',
		'Dice not included in digital form. Please imagine them.',
		'Pro tip: Blame the dice, not your math skills.',
		'Fun fact: This game pairs well with snacks.',
		'Every roll is a plot twist in disguise.',
		'Remember: The real box was the friends we shut along the way.',
		'If you win, bragging rights are included—free of charge.',
		'Math teachers recommend this game for extra credit (not really).',
		'Rolling dice is the original random number generator.',
		'The box isn’t the only thing getting shut—so are your hopes!',
		'Try not to shut your laptop in frustration.',
		'Rumor has it, lucky socks improve your odds.',
		'No boxes were harmed in the making of this game.',
		'If at first you don’t succeed, blame the algorithm.',
		'This game is 90% luck, 10% questionable decisions.',
		'The dice are watching. Roll wisely.',
		'Shut the Box: Because spreadsheets aren’t fun enough.',
		'May your rolls be high and your sums be exact.',
		'The only thing harder than shutting the box is explaining the rules.',
		'If you lose, just call it a “strategic retreat.”',
		'Box not included. Batteries not required.',
		'Every number you shut is a small victory.',
		'The suspense is real. The box is virtual.',
		'You can’t cheat, but you can try.',
		'Some say the box is a metaphor for life.',
		'Rolling dice: the original fidget spinner.'
	];

	function getQuirkiness(): string {
		return quirkyLines[Math.floor(Math.random() * quirkyLines.length)];
	}
</script>

<div
	class="flex min-h-screen flex-col items-center justify-center overflow-x-hidden bg-gray-800 py-4 text-white"
>
	{#if showConfetti}
		<div class="z-50">
			<Confetti
				amount={250}
				size={window.innerWidth < 640 ? 25 : 75}
				x={window.innerWidth < 640 ? [-2.5, 2.5] : [-8, 8]}
				y={window.innerWidth < 640 ? [0.75, -1] : [1, -1.25]}
				fallDistance={'75vh'}
				duration={3000}
			/>
		</div>
	{/if}
	<Modal {showModal} onNewGame={newGame} title={modalTitle} message={modalMessage} />
	<h1 class="mb-4 text-4xl font-bold sm:text-5xl">Shut The Box</h1>
	<p class="text-md mb-4 max-w-xs text-center sm:mb-8 sm:max-w-md sm:text-lg">{getQuirkiness()}</p>

	<div class="w-full max-w-4xl rounded-lg bg-gray-900 p-4 shadow-lg sm:p-8">
		<div class="mb-8 grid grid-cols-6 gap-2 sm:grid-cols-8 lg:grid-cols-12">
			{#each numbers as number}
				<button
					class="h-12 w-12 rounded-md text-xl font-bold transition-all duration-200 sm:h-16 sm:w-16 sm:text-2xl {number.active
						? 'bg-blue-600 hover:bg-blue-700'
						: 'cursor-not-allowed bg-gray-700 text-gray-500'} {selectedNumbers.includes(
						number.value
					)
						? 'ring-4 ring-yellow-400'
						: ''}"
					on:click={() => toggleNumber(number.value)}
					disabled={!number.active}
				>
					{number.value}
				</button>
			{/each}
		</div>

		<div class="mb-8 flex items-center justify-center">
			{#each dice as die, index (index)}
				<div
					class="mr-4 flex h-16 w-16 items-center justify-center rounded-lg border-4 border-gray-500 bg-white text-4xl font-bold text-gray-900 sm:h-20 sm:w-20 sm:text-5xl"
					animate:flip={{ duration: 150, easing: quintOut }}
				>
					{die}
				</div>
			{/each}
		</div>

		<div
			class="relative flex flex-col space-y-4 sm:flex-row sm:justify-center sm:space-y-0 sm:space-x-4"
		>
			{#if !diceRolled && !gameOver}
				<button
					class="rounded-lg bg-green-600 px-6 py-3 text-xl font-bold text-white hover:bg-green-700"
					on:click={rollDiceWithAnimation}
					disabled={rolling}
				>
					{#if rolling}Rolling...{:else}Roll Dice{/if}
				</button>
			{:else if !gameOver}
				<button
					class="rounded-lg bg-yellow-500 px-6 py-3 text-xl font-bold text-white hover:bg-yellow-600"
					on:click={shutTheBox}
					disabled={selectedNumbers.length === 0}
				>
					Shut The Box
				</button>
			{/if}

			<button
				class="rounded-lg bg-gray-600 px-6 py-3 text-xl font-bold text-white hover:bg-gray-700"
				on:click={newGame}
			>
				New Game
			</button>

			<div
				class="absolute -top-12 left-1/2 -translate-x-1/2 rounded bg-red-600 px-3 py-2 text-sm text-white shadow-md transition-opacity duration-300 {showInvalidSelectionTooltip
					? 'opacity-100'
					: 'pointer-events-none opacity-0'}"
			>
				Selected numbers must equal the dice roll.
			</div>
		</div>
		<div class="mt-4 flex justify-center space-x-2">
			{#each Array(maxStrikes) as _, i}
				<div
					class="flex h-8 w-8 items-center justify-center rounded-md text-xl font-bold text-white {i <
					strikes
						? 'bg-red-600'
						: 'bg-gray-600'}"
				>
					{#if i < strikes}X{/if}
				</div>
			{/each}
		</div>
	</div>

	<div class="mt-8 w-full max-w-4xl rounded-lg bg-gray-900 p-4 shadow-lg sm:p-8">
		<h2 class="mb-4 text-3xl font-bold">Rules of Shut The Box</h2>
		<ul class="list-disc space-y-2 pl-5">
			<li>The game starts with the numbers 1-12 available.</li>
			<li>
				Click "Roll Dice" to roll two six-sided dice. The sum of the dice is the target number.
			</li>
			<li>
				Select one or more available numbers that add up to the target number. For example, if you
				roll a 7, you can select 7, or 1 and 6, or 2 and 5, or 3 and 4, or 1, 2 and 4.
			</li>
			<li>
				Click "Shut The Box" to confirm your selection. The selected numbers are now unavailable.
			</li>
			<li>The goal is to make all numbers unavailable. If you do, you win!</li>
			<li>
				If you cannot make a move with the available numbers, you get a strike. The game ends after
				3 strikes. Your score is the sum of the numbers that are still available. A lower score is
				better.
			</li>
		</ul>
	</div>
</div>
