<script lang="ts">
	import "../app.css";
	let { children } = $props();
</script>

<svelte:head>
  <title>typebeat</title>
</svelte:head>

<div class="w-full h-screen">
  {@render children()}
</div>
