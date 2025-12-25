<script lang="ts">
	import * as Card from '$lib/components/ui/card/index.js';
	import * as Tabs from '$lib/components/ui/tabs/index.js';
	import { Input } from '@/components/ui/input/index.js';
	import { Label } from '@/components/ui/label/index.js';
	import LeafPattern from '@/patterns/LeafPattern.svelte';
	import * as ToggleGroup from '$lib/components/ui/toggle-group/index.js';
	import { Canvas } from '@threlte/core';
	import Scene from '@/Scene.svelte';
	import { onMount } from 'svelte';
	import { toPng } from 'html-to-image';
	import Button from '@/components/ui/button/button.svelte';
	// import printJS from 'print-js';

	const patterns = [
		'3.jpg',
		'4.jpg',
		'5.jpg',
		'6.svg',
		'7-cropped.svg',
		'8.svg',
		'9.svg',
		'10.svg',
		'11.svg'
	];

	const colors = [{ bg: '#9678b6' }, { bg: '#873f2b' }, { bg: '#3eb489' }];
	const styles = ['striped', 'boxed', 'portrait'];

	let productName = $state('Peppermint');
	let scientificName = $state('Lavendula Angustfolia');
	let selectedPattern = $state(patterns[5]);
	let selectedColor = $state(colors[0]);
	let selectedStyle = $state(styles[0]);

	let labelNode: HTMLElement | undefined;
	let label: undefined | string = $state();

	async function recalculate() {
		console.log('recalculating');
		label = await toPng(labelNode!, { canvasWidth: 1200 });
	}

	onMount((dependencies) => {
		recalculate();
	});

	$effect(() => {
		recalculate({ productName, scientificName, selectedColor, selectedPattern, selectedStyle });
	});

	async function print() {
		const printJS = (await import('print-js')).default;
		printJS({
			printable: label,
			type: 'image',
			base64: true
		});
	}
</script>

<svelte:head>
	<title>Ettara label generator</title>
</svelte:head>

<main class="flex flex-row gap-5 overflow-y-auto p-5">
	<Card.Root class="w-96 shrink-0">
		<Card.Header>
			<Card.Title>Configure Label</Card.Title>
		</Card.Header>
		<Card.Content>
			<p onclick={print}>Print</p>
			<div class="grid gap-6">
				<div class="grid gap-2">
					<Label>Product Name</Label>
					<Input bind:value={productName}></Input>
				</div>

				<div class="grid gap-2">
					<Label>Scientific Name</Label>
					<Input bind:value={scientificName}></Input>
				</div>

				<div class="grid gap-2">
					<Label>Style</Label>
					<ToggleGroup.Root
						bind:value={selectedStyle}
						type="single"
						spacing={4}
						class="flex flex-wrap"
					>
						{#each styles as style, i}
							<ToggleGroup.Item
								value={style}
								class="overflow-hidden rounded border   p-5 data-[state=on]:bg-primary  data-[state=on]:text-primary-foreground"
							>
								{style}
							</ToggleGroup.Item>
						{/each}
					</ToggleGroup.Root>
				</div>

				<div class="grid gap-2">
					<Label>Pattern</Label>
					<ToggleGroup.Root
						bind:value={selectedPattern}
						type="single"
						spacing={4}
						class="flex flex-wrap"
					>
						{#each patterns as pattern, i}
							<ToggleGroup.Item
								value={pattern}
								class="h-20 w-20 overflow-hidden rounded border-zinc-800  p-0 data-[state=on]:border-4"
							>
								<img
									src={`/patterns/${pattern}`}
									alt=""
									class="size-full object-cover object-center"
								/>
							</ToggleGroup.Item>
						{/each}
					</ToggleGroup.Root>
				</div>

				<div class="grid gap-2">
					<Label>Color</Label>
					<ToggleGroup.Root
						bind:value={selectedColor}
						type="single"
						spacing={4}
						class="flex flex-wrap"
					>
						{#each colors as color, i}
							<ToggleGroup.Item
								value={color}
								class="h-7 w-7 overflow-hidden rounded border-zinc-800  p-0 data-[state=on]:border-2"
							>
								<div
									class:border-2={selectedColor.bg === color.bg}
									class="size-full border-zinc-800"
									style:background-color={color.bg}
								></div>
							</ToggleGroup.Item>
						{/each}
					</ToggleGroup.Root>
				</div>
			</div>
		</Card.Content>
	</Card.Root>

	<div class="grid gap-6">
		<Card.Root class="shrink-0">
			<Card.Header class="flex items-center justify-between">
				<Card.Title>Preview</Card.Title>
				<Card.Action>
					<Button variant="outline" onclick={print}>Print Label</Button>
				</Card.Action>
			</Card.Header>
			<Card.Content>
				<div
					class="relative flex h-[16cm] w-[28cm] items-center justify-center"
					bind:this={labelNode}
				>
					<div
						class="text-red absolute inset-0 h-full w-full bg-repeat opacity-70"
						style:background-color={selectedColor.bg}
					>
						<div
							class="size-full"
							class:invert={selectedPattern.endsWith('svg')}
							style:background-image={`url('/patterns/${selectedPattern}')`}
							style:background-size="140px"
						></div>
						<!-- <LeafPattern /> -->
					</div>
					<div
						class=" relative z-10 flex h-[88%] w-full justify-center bg-amber-50! p-10"
						class:w-[30%]!={selectedStyle === 'boxed' || selectedStyle === 'portrait'}
						class:-mt-20!={selectedStyle === 'portrait'}
						class:-mb-40!={selectedStyle === 'portrait'}
					>
						<div class=" w-96 justify-center text-center">
							<img src="/logo.png" class="mx-auto w-26 brightness-0" alt="" />
							<h1
								class="mt-8 font-sans text-4xl font-semibold uppercase"
								style:color={selectedColor.bg}
							>
								{productName}
							</h1>

							<p class="font-serif italic">{scientificName}</p>

							<div class="mt-72">
								<p class="font-semibold">1 LITRE / 33.8 FL OZ ℮</p>
								<p class="font-medium">Potent raw material. For professional use only.</p>
							</div>
						</div>
					</div>
				</div>
			</Card.Content>
		</Card.Root>

		<Card.Root class="shrink-0">
			<Card.Header>
				<Card.Title>3D Preview</Card.Title>
			</Card.Header>

			<Card.Content class="">
				<div class="h-[16cm] bg-zinc-800">
					<Canvas>
						{#key label}
							<Scene {label} />
						{/key}
					</Canvas>
				</div>
			</Card.Content>
		</Card.Root>
	</div>
</main>
