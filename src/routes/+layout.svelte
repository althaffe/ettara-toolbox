<script>
	import '../app.css';
	import '@fontsource-variable/source-sans-3';

	/** @type {{children: import('svelte').Snippet}} */
	let { children } = $props();
</script>

<div class="app text-gray-900">
	{@render children()}
</div>
