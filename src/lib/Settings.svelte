<script lang="ts">
	let {
		numberOfDice = $bindable(),
		maxStrikes = $bindable()
	}: {
		numberOfDice: number;
		maxStrikes: number;
	} = $props();
</script>

<div class="w-full max-w-4xl rounded-lg bg-gray-900 p-4 shadow-lg sm:p-8">
	<h2 class="mb-4 text-center text-3xl font-bold">Settings</h2>
	<div class="flex flex-col space-y-8 space-x-8">
		<div class="flex items-center">
			<label for="dice-select" class="mr-4 text-xl">Number of Dice:</label>
			<select
				id="dice-select"
				class="rounded-lg bg-gray-800 px-4 py-2 text-xl text-white"
				bind:value={numberOfDice}
			>
				<option value={1}>1</option>
				<option value={2}>2</option>
				<option value={3}>3</option>
				<option value={4}>4</option>
			</select>
		</div>
		<div class="flex items-center">
			<label for="strikes-select" class="mr-4 text-xl">Number of Strikes:</label>
			<select
				id="strikes-select"
				class="rounded-lg bg-gray-800 px-4 py-2 text-xl text-white"
				bind:value={maxStrikes}
			>
				<option value={0}>0</option>
				<option value={1}>1</option>
				<option value={2}>2</option>
				<option value={3}>3</option>
				<option value={4}>4</option>
				<option value={5}>5</option>
			</select>
		</div>
	</div>
</div>
