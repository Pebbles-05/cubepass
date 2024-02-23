<script lang="ts">
	import Icon from '@iconify/svelte';
	export let password: string = '',
		length: number = 12,
		includeUppercase: boolean = true,
		includeNumbers: boolean = true,
		includeLowercase: boolean = true,
		includeSpecialChars: boolean = true;

	const copyInputValue = (): void => {
		// Access the value from the input field using the bound variable
		const copiedValue = password;
		// Now you can use copiedValue wherever you need it
		console.log('Copied value:', copiedValue);
		// You can also copy it to the clipboard
		navigator.clipboard
			.writeText(copiedValue)
			.then(() => {
				console.log('Copied to clipboard!');
			})
			.catch((err) => {
				console.error('Failed to copy: ', err);
			});
	};
	// Function to get a random character from a string
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
		// Define character sets for each character type
		const uppercaseChars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
		const lowercaseChars = 'abcdefghijklmnopqrstuvwxyz';
		const numberChars = '0123456789';
		const specialChars = '!@#$%^&*()_+{}[]|:;"\'<>,.?/';

		let newPassword = '',
			allChars = '';

		// Ensure at least one character from each selected character type
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
		const remainingLength = length - newPassword.length; // Remaining length for random characters

		// Generate remaining random characters
		for (let i = 0; i < remainingLength; i++) {
			newPassword += allChars.charAt(Math.floor(Math.random() * allChars.length));
		}

		// Shuffle the password to ensure randomness
		password = newPassword
			.split('')
			.sort(() => Math.random() - 0.5)
			.join('');
	};

	$: generatePassword(
		length,
		includeUppercase,
		includeLowercase,
		includeNumbers,
		includeSpecialChars
	);
</script>

<div class="w-full flex outline outline-[#cdd6f4] rounded outline-2 text-2xl">
	<input
		class="w-full bg-transparent outline-none py-4 pl-4 pr-2 h-max"
		type="text"
		bind:value={password}
	/>
	<div class="cursor-pointer px-2 w-max" on:click={copyInputValue}>
		<Icon icon="tabler:copy" class="h-full w-7" />
	</div>

	<div class="cursor-pointer pl-2 pr-4 w-max" on:click={() => generatePassword()}>
		<Icon icon="grommet-icons:power-reset" class="h-full w-7" />
	</div>
</div>
