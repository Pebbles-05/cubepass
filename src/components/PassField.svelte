<script lang="ts">
	import Icon from '@iconify/svelte';
	export let password: string = '',
		length: number = 12,
		includeUppercase: boolean = true,
		includeNumbers: boolean = true,
		includeLowercase: boolean = true,
		includeSpecialChars: boolean = true,
		isCoppied: boolean = false;

	const copyInputValue = (): void => {
		const copiedValue = password;
		navigator.clipboard
			.writeText(copiedValue)
			.then(() => {
				isCoppied = true;
				setTimeout(() => {
					isCoppied = false;
					console.log('running');
				}, 2000);
				console.log('Copied to clipboard!');
			})
			.catch((err) => {
				console.error('Failed to copy: ', err);
			});
	};
	const getRandomChar = (str: string): string => {
		return str.charAt(Math.floor(Math.random() * str.length));
	};

	const generatePassword = (
		passwordLength: number = length,
		haveUppercase: boolean = includeUppercase,
		haveLowercase: boolean = includeLowercase,
		haveNumbers: boolean = includeNumbers,
		haveSpecialChars: boolean = includeSpecialChars
	): void => {
		const uppercaseChars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
		const lowercaseChars = 'abcdefghijklmnopqrstuvwxyz';
		const numberChars = '0123456789';
		const specialChars = '!@#$%^&*()_+{}[]|:;"\'<>,.?/';

		let newPassword = '',
			allChars = '';

		if (haveUppercase) {
			allChars += uppercaseChars;
			newPassword += getRandomChar(uppercaseChars);
		}
		if (haveLowercase) {
			allChars += lowercaseChars;
			newPassword += getRandomChar(lowercaseChars);
		}
		if (haveNumbers) {
			allChars += numberChars;
			newPassword += getRandomChar(numberChars);
		}
		if (haveSpecialChars) {
			allChars += specialChars;
			newPassword += getRandomChar(specialChars);
		}

		if (allChars === '' && !passwordLength) return;
		const remainingLength = length - newPassword.length;

		for (let i = 0; i < remainingLength; i++) {
			newPassword += allChars.charAt(Math.floor(Math.random() * allChars.length));
		}

		password = newPassword
			.split('')
			.sort(() => Math.random() - 0.5)
			.join('')
			.slice(0, passwordLength);
	};

	$: generatePassword(
		length,
		includeUppercase,
		includeLowercase,
		includeNumbers,
		includeSpecialChars
	);
</script>

<div class="w-full flex outline outline-[#cdd6f4] rounded outline-2 text-xl lg:text-2xl">
	<input
		class="w-full bg-transparent outline-none py-2 pl-2 pr-1 h-max lg:py-4 lg:pr-2 lg:pl-4"
		type="text"
		bind:value={password}
	/>
	<div
		class="cursor-pointer px-1 lg:px-2 w-max {isCoppied ? 'pointer-events-none' : ''}"
		on:click={copyInputValue}
		title="copy"
	>
		<Icon icon={isCoppied ? 'charm:tick-double' : 'tabler:copy'} class="h-full w-5 lg:w-7" />
	</div>

	<div
		class="cursor-pointer pl-1 pr-2 lg:pl-2 lg:pr-4 w-max"
		title="generate"
		on:click={() => generatePassword()}
	>
		<Icon icon="grommet-icons:power-reset" class="h-full w-5 lg:w-7" />
	</div>
</div>
