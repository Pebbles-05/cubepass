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
		isCoppied: boolean = false,
		passwordStrengthIndicator: passwordStrengthStateType;
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
			allChars = '',
			complexityCount = 0;

		if (haveUppercase) {
			allChars += uppercaseChars;
			newPassword += getRandomChar(uppercaseChars);
			complexityCount++;
		}
		if (haveLowercase) {
			allChars += lowercaseChars;
			newPassword += getRandomChar(lowercaseChars);
			complexityCount++;
		}
		if (haveNumbers) {
			allChars += numberChars;
			newPassword += getRandomChar(numberChars);
			complexityCount++;
		}
		if (haveSpecialChars) {
			allChars += specialChars;
			newPassword += getRandomChar(specialChars);
			complexityCount++;
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
		checkPasswordStrength(password, complexityCount);
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
	const checkPasswordStrength = (password: string, complexityCount: number = 0) => {
		const minLength = 8;
		if (password.length < minLength || commonPasswords.includes(password.toLowerCase())) {
			passwordStrengthIndicator = passwordStrengthState.weak;
			return;
		}

		const minComplexityCount = 3;
		if (complexityCount === 0) {
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
		}

		if (complexityCount < minComplexityCount && password.length < 16) {
			passwordStrengthIndicator = passwordStrengthState.fair;
			return;
		}

		passwordStrengthIndicator = passwordStrengthState.strong;
	};
</script>

<div class="flex flex-col gap-2">
	<div class="flex gap-1 items-center text-sm lg:text-xl">
		<Icon icon={passwordStrengthIndicator.icon} />
		{passwordStrengthIndicator.title}
	</div>
	<div class="w-full flex outline outline-[#cdd6f4] rounded outline-2 text-2xl lg:text-4xl">
		<input
			class="w-full bg-transparent outline-none h-max py-4 pr-2 pl-4"
			type="text"
			bind:value={password}
			on:input={() => checkPasswordStrength(password)}
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
