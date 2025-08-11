<script lang="ts">
	import '../app.css';
	import favicon from '$lib/assets/favicon.png';

	let { children } = $props();
</script>

<svelte:head>
	<title>Shut The Box</title>
	<meta
		name="description"
		content="A fun and challenging dice game. Try to shut all the numbers to win!"
	/>
	<link rel="icon" href={favicon} />

	<!-- Facebook Meta Tags -->
	<meta property="og:url" content="https://karsten-larson.github.io/shut-the-box/" />
	<meta property="og:type" content="website" />
	<meta property="og:title" content="Shut The Box" />
	<meta
		property="og:description"
		content="A fun and challenging dice game. Try to shut all the numbers to win!"
	/>
	<meta property="og:image" content={favicon} />

	<!-- Twitter Meta Tags -->
	<meta name="twitter:card" content="summary_large_image" />
	<meta property="twitter:domain" content="karsten-larson.github.io" />
	<meta property="twitter:url" content="https://karsten-larson.github.io/shut-the-box/" />
	<meta name="twitter:title" content="Shut The Box" />
	<meta
		name="twitter:description"
		content="A fun and challenging dice game. Try to shut all the numbers to win!"
	/>
	<meta name="twitter:image" content={favicon} />
</svelte:head>

{@render children?.()}
