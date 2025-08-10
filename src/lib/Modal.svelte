
<script lang="ts">
  export let showModal: boolean;
  export let onNewGame: () => void;
</script>

{#if showModal}
  <div class="fixed inset-0 z-50 flex items-center justify-center bg-black bg-opacity-50">
    <div class="rounded-lg bg-gray-800 p-8 text-center shadow-lg">
      <h2 class="mb-4 text-4xl font-bold text-white">You Win!</h2>
      <p class="mb-6 text-2xl text-white">Congratulations, you shut the box!</p>
      <button
        class="rounded-lg bg-green-600 px-6 py-3 text-xl font-bold text-white hover:bg-green-700"
        on:click={onNewGame}
      >
        New Game
      </button>
    </div>
  </div>
{/if}
