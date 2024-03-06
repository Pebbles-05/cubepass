<script lang="ts">
	import { passwordStrengthState } from '../enums/passwordStrengthState';
	import Icon from '@iconify/svelte';
	import type { passwordStrengthStateType } from '../types/types';
	import commonPasswords from '../enums/commonPasswords.json';

	export let password: string = '',
		length: number = 16,
		includeUppercase: boolean = true,
		includeNumbers: boolean = true,
		includeLowercase: boolean = true,
		includeSpecialChars: boolean = true,
		isCoppied: boolean = false;
	const uppercaseChars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ',
		lowercaseChars = 'abcdefghijklmnopqrstuvwxyz',
		numberChars = '0123456789',
		specialChars = '!@#$%^&*()_+{}[]|:;"\'<>,.?/';

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
		const remainingLength = passwordLength - newPassword.length;

		for (let i = 0; i < remainingLength; i++) {
			newPassword += getRandomChar(allChars);
		}

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

	const checkCommonElements = (password: string, charList: string): boolean => {
		const passwordSet = new Set(password);
		const charListSet = new Set(charList);
		return [...passwordSet].some((letter) => charListSet.has(letter));
	};
	const checkPasswordStrength = (password: string): passwordStrengthStateType => {
		const minLength = 8;
		if (password.length < minLength || commonPasswords.includes(password.toLowerCase())) {
			return passwordStrengthState.weak;
		}

		const minComplexityCount = 3;
		let complexityCount = 0;
		if (checkCommonElements(password, uppercaseChars)) {
			complexityCount++;
		}
		if (checkCommonElements(password, lowercaseChars)) {
			complexityCount++;
		}
		if (checkCommonElements(password, numberChars)) {
			complexityCount++;
		}
		if (checkCommonElements(password, specialChars)) {
			complexityCount++;
		}

		if (complexityCount < minComplexityCount && password.length < 16) {
			return passwordStrengthState.fair;
		}

		return passwordStrengthState.strong;
	};
	console.log(commonPasswords);
	$: passwordStrengthIndicator = checkPasswordStrength(password);
</script>

<div class="flex flex-col gap-2">
	<div class="flex gap-1 items-center text-sm">
		<Icon icon={passwordStrengthIndicator.icon} />
		{passwordStrengthIndicator.title}
	</div>
	<div class="w-full flex outline outline-[#cdd6f4] rounded outline-2 text-2xl">
		<input
			class="w-full bg-transparent outline-none h-max py-4 pr-2 pl-4"
			type="text"
			bind:value={password}
		/>
		<div
			class="cursor-pointer px-2 w-max {isCoppied ? 'pointer-events-none' : ''}"
			on:click={copyInputValue}
			title="copy"
		>
			<Icon icon={isCoppied ? 'charm:tick-double' : 'tabler:copy'} class="h-full w-7" />
		</div>

		<div
			class="cursor-pointer pl-2 pr-4 w-max"
			title="generate"
			on:click={() => generatePassword()}
		>
			<Icon icon="grommet-icons:power-reset" class="h-full w-7" />
		</div>
	</div>
</div>
