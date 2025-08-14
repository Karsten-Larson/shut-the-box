<script lang="ts">
	let { number, rolling }: { number: number; rolling: boolean } = $props();

	function rollingAnimation(node: HTMLElement) {
		const rollNumbers = [1, 2, 3, 4, 5, 6];
		let running = true;

		function roll() {
			if (!running) return;
			const randomNum = rollNumbers[Math.floor(Math.random() * rollNumbers.length)];
			node.textContent = String(randomNum);
			requestAnimationFrame(roll);
		}

		roll();

		return {
			destroy() {
				running = false;
			}
		};
	}
</script>

{#if number > 0}
	{#if rolling}
		<div
			class="mr-4 flex h-16 w-16 items-center justify-center rounded-lg border-4 border-gray-500 bg-white text-4xl font-bold text-gray-900 sm:h-20 sm:w-20 sm:text-5xl"
			use:rollingAnimation
		></div>
	{:else}
		<div
			class="mr-4 flex h-16 w-16 items-center justify-center rounded-lg border-4 border-gray-500 bg-white text-4xl font-bold text-gray-900 sm:h-20 sm:w-20 sm:text-5xl"
		>
			{number}
		</div>
	{/if}
{:else}
	<div
		class="mr-4 flex h-16 w-16 items-center justify-center rounded-lg border-4 border-dashed border-gray-500 bg-gray-800 text-4xl font-bold text-gray-900 sm:h-20 sm:w-20 sm:text-5xl"
	></div>
{/if}
