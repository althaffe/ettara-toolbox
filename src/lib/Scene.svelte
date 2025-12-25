<script>
	import * as THREE from 'three';
	import { T, useRenderer } from '@threlte/core';
	import { ContactShadows, Environment, OrbitControls } from '@threlte/extras';

	import { onMount } from 'svelte';
	const { label } = $props();
	import {
		DoubleSide,
		TextureLoader,
		MeshBasicMaterial,
		SRGBColorSpace,
		RepeatWrapping
	} from 'three';
	const renderer = useRenderer();

	onMount(() => {
		// renderer.setPixerRatio(window.devicePixelRatio);
		// renderer.renderer.setPixelRatio(window.devicePixelRatio);
		renderer.renderer.toneMapping = THREE.NeutralToneMapping;
		renderer.renderer.toneMappingExposure = 1.1;
	});

	const texture = $derived.by(async () => {
		// const png = await svgToDataUrl(lavendarLabel, { format: 'png', width: 1500 });
		if (!label) return null;

		const loader = new TextureLoader();
		const texture = loader.load(label);
		texture.colorSpace = SRGBColorSpace;
		// texture.flipY = false;
		// texture.anisotropy = renderer.renderer.getMaxAnisotropy();
		texture.generateMipmaps = false;
		return texture;
	});
</script>

<T.PerspectiveCamera makeDefault position={[80, 20, 20]} fov={20}>
	<OrbitControls enableDamping autoRotate autoRotateSpeed={0} target.x={0.1} target.y={0.3} />
</T.PerspectiveCamera>
<T.Group rotation.y={4.5} position={[0, 0, 0]} scale={1} dispose={false}>
	<T.Mesh>
		<T.CylinderGeometry args={[4.4, 4.4, 18]} />
		<T.MeshPhysicalMaterial metalness={0.7} roughness={0.5} />
	</T.Mesh>
	{#await texture then map}
		<T.Mesh>
			<T.CylinderGeometry args={[4.5, 4.5, 16]} />
			<T.MeshPhysicalMaterial metalness={0.5} roughness={0.7} {map} />
		</T.Mesh>
	{/await}
</T.Group>
<!-- <Serum {label} /> -->
<Environment url="/hall.hdr" />

<ContactShadows opacity={0.4} />
