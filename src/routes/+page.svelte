<script lang="ts">
	import Icon from '@iconify/svelte';
	import '../app.css';
	import PassField from '../components/PassField.svelte';
	import SettingsDock from '../components/SettingsDock.svelte';
	import type { optionsType } from '../types/types';

	let options: optionsType[],
		length: number = 16;
	$: isOptionsAvailable = Array.isArray(options) && options?.length != 0;
</script>

<main
	class="w-screen min-h-screen overflow-auto bg-[#272829] text-[#cdd6f4] flex flex-col items-center p-5 pt-14 gap-20"
>
	<h1 class="text-[#cdd6f4] text-4xl lg:text-5xl underline underline-offset-8">CUBEPASS</h1>
	<div class="w-[80%] lg:w-[40%] flex flex-col gap-2 lg:gap-4">
		<PassField
			includeUppercase={isOptionsAvailable && options[0]?.value}
			includeLowercase={isOptionsAvailable && options[1]?.value}
			includeNumbers={isOptionsAvailable && options[2].value}
			includeSpecialChars={isOptionsAvailable && options[3]?.value}
			bind:length
		/>
		<SettingsDock bind:options bind:length />
	</div>
	<a href="https://github.com/Pebbles-05/cubepass" target="_blank" class="mt-auto self-end"
		><Icon icon="jam:github" class="w-6 h-6 lg:w-8 lg:h-8" /></a
	>
</main>
