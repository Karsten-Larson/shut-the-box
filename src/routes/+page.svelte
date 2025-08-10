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
			score = openNumbers.reduce((a, b) => a + b, 0);
			gameOver = true;
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
				showConfetti = true;
				setTimeout(() => {
					showConfetti = false;
					showModal = true;
				}, 3000);
			}
		} else {
			showInvalidSelectionTooltip = true;
			setTimeout(() => {
				showInvalidSelectionTooltip = false;
			}, 3000);
		}
	}
</script>

<div class="flex min-h-screen flex-col items-center justify-center bg-gray-800 py-4 text-white">
	{#if showConfetti}
		<Confetti />
	{/if}
	<Modal {showModal} onNewGame={newGame} />
	<h1 class="mb-4 text-4xl font-bold sm:text-5xl">Shut The Box</h1>
	<p class="mb-8 text-lg">Svelte 5 + Tailwind CSS</p>

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

		<div class="relative flex flex-col space-y-4 sm:flex-row sm:justify-center sm:space-x-4 sm:space-y-0">
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
				class="absolute -top-12 left-1/2 -translate-x-1/2 rounded bg-red-600 px-3 py-2 text-sm text-white shadow-md transition-opacity duration-300 {showInvalidSelectionTooltip ? 'opacity-100' : 'opacity-0 pointer-events-none'}"
			>
				Selected numbers must equal the dice roll.
			</div>
		</div>
	</div>

	{#if gameOver && !showModal}
		<div class="mt-8 text-center">
			<h2 class="mb-2 text-4xl font-bold">{score === 0 ? 'You Win!' : 'Game Over!'}</h2>
			<p class="text-2xl">{score === 0 ? 'You shut the box!' : `Your score is ${score}`}</p>
		</div>
	{/if}

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
				If you cannot make a move with the available numbers, the game is over. Your score is the
				sum of the numbers that are still available. A lower score is better.
			</li>
		</ul>
	</div>
</div>
