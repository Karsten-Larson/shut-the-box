<script lang="ts">
	export let showModal: boolean;
	export let onNewGame: () => void;
	export let title: string;
	export let message: string;
</script>

{#if showModal}
	<div class="bg-opacity-10 fixed inset-0 z-50 flex items-center justify-center bg-black">
		<div class="rounded-lg bg-gray-800 p-8 text-center shadow-lg">
			<h2 class="mb-4 text-4xl font-bold text-white">{title}</h2>
			<p class="mb-6 text-2xl text-white">{@html message}</p>
			<button
				class="rounded-lg bg-green-600 px-6 py-3 text-xl font-bold text-white hover:bg-green-700"
				on:click={onNewGame}
			>
				New Game
			</button>
		</div>
	</div>
{/if}
