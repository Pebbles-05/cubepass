<script lang="ts">
	import '../app.css';
	import PassField from '../components/PassField.svelte';
	import SettingsDock from '../components/SettingsDock.svelte';
	import type { optionsType } from '../types/types';

	let options: optionsType[],
		length: number = 12;
	$: isOptionsAvailable = Array.isArray(options) && options?.length != 0;
</script>

<main
	class="w-screen h-screen overflow-auto bg-[#272829] text-[#cdd6f4] flex flex-col justify-center items-center"
>
	<div class="w-[40%] flex flex-col gap-4">
		<PassField
			includeUppercase={isOptionsAvailable && options[0]?.value}
			includeLowercase={isOptionsAvailable && options[1]?.value}
			includeNumbers={isOptionsAvailable && options[2].value}
			includeSpecialChars={isOptionsAvailable && options[3]?.value}
			bind:length
		/>
		<SettingsDock bind:options bind:length />
	</div>
	<slot />
</main>
