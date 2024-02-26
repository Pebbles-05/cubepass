<script lang="ts">
	import { v4 as uuidv4 } from 'uuid';
	import type { optionsType } from '../types/types.ts';
	import { Options } from '../enums/options';
	import Icon from '@iconify/svelte';
	export let length: number = 12,
		options: optionsType[] = [
			{
				id: uuidv4(),
				title: Options.UPPERCASE,
				value: true
			},
			{
				id: uuidv4(),
				title: Options.LOWERCASE,
				value: true
			},
			{
				id: uuidv4(),
				title: Options.NUMBERS,
				value: true
			},
			{
				id: uuidv4(),
				title: Options.SPECIAL_CHARACTERS,
				value: true
			}
		];
	const handleLengthInput = (e) => {
		const lengthInputInNumber = Number(e.target.value);
		if (e.key == 'Enter') {
			length = lengthInputInNumber < 4 ? 4 : lengthInputInNumber > 50 ? 50 : lengthInputInNumber;
			e.target.value = length;
		}
	};
</script>

<div class="w-full flex flex-col lg:flex-row gap-2 lg:gap-4 text-[20px]">
	<form class="flex flex-col w-full gap-2" on:submit|preventDefault>
		<span>Password length :</span>
		<div class="w-full flex gap-2">
			<input
				type="text"
				value={length}
				class="w-9 h-max outline outline-2 outline-[#cdd6f4] rounded px-2 py-1 bg-transparent"
				on:keypress={handleLengthInput}
				on:input={(e) => (e.target.value = e.target.value.replace(/\D/g, ''))}
			/>
			<input
				type="range"
				name="length"
				min={1}
				max={50}
				bind:value={length}
				class="accent-[#cdd6f4] w-full"
			/>
		</div>
	</form>
	<div class="w-full flex flex-col gap-2">
		{#each options as option (option.id)}
			<label class="flex gap-2 items-start cursor-pointer" title={option.title}>
				<Icon
					icon={option.value
						? 'fluent:checkbox-indeterminate-16-filled'
						: 'fluent:checkbox-unchecked-16-filled'}
					class="w-6 h-6 shrink-0 mt-[0.5px]"
				/>
				<input
					type="checkbox"
					class="hidden"
					name={option.title}
					bind:checked={option.value}
					disabled={options
						.filter((item) => item.id !== option.id)
						.every((item) => item.value === false)}
				/>
				{option.title}
			</label>
		{/each}
	</div>
</div>
