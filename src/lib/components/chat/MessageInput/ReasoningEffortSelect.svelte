<script lang="ts">
	import { getContext } from 'svelte';
	import { settings, user } from '$lib/stores';
	import Dropdown from '$lib/components/common/Dropdown.svelte';
	import DropdownMenu from '$lib/components/common/DropdownMenu.svelte';
	import Tooltip from '$lib/components/common/Tooltip.svelte';
	import Check from '$lib/components/icons/Check.svelte';
	import LightBulb from '$lib/components/icons/LightBulb.svelte';

	const i18n: any = getContext('i18n');

	// 与“控制 → 高级参数”里的推理努力共用 params.reasoning_effort；null 表示不下发，沿用模型/用户默认
	export let params: Record<string, any> = {};

	// 只列各家提供商都接受的档位；xhigh/max 等仍可在高级参数里手填
	const EFFORT_OPTIONS = [
		{ value: null, labelKey: 'Default' },
		{ value: 'low', labelKey: 'Low' },
		{ value: 'medium', labelKey: 'Medium' },
		{ value: 'high', labelKey: 'High' }
	];

	$: permitted =
		$user?.role === 'admin' ||
		(($user?.permissions?.chat?.controls ?? true) && ($user?.permissions?.chat?.params ?? true));
	$: current = params?.reasoning_effort ?? null;
	$: currentOption = EFFORT_OPTIONS.find((option) => option.value === current);
	// 高级参数里手填了列表之外的值（如 xhigh）时原样显示，避免下拉框谎称“默认”
	$: currentLabel = currentOption ? $i18n.t(currentOption.labelKey) : current;
	$: rowClass = `focus-ring flex h-[1.6875rem] w-full cursor-pointer select-none items-center gap-2 rounded-xl px-2 text-left text-[0.8125rem] font-normal text-gray-700 outline-hidden transition-colors duration-75 dark:text-gray-100 ${$settings?.highContrastMode ? 'hover:bg-gray-200! dark:hover:bg-gray-800!' : 'hover:bg-gray-50/40! dark:hover:bg-gray-800/40!'}`;
	$: selectedClass = $settings?.highContrastMode
		? 'bg-gray-200 dark:bg-gray-800'
		: 'bg-gray-50/70 dark:bg-gray-800/60';

	const select = (value: string | null) => {
		params = { ...params, reasoning_effort: value };
	};
</script>

{#if permitted}
	<Dropdown align="end" visualViewportAware closeOnSelect>
		<Tooltip
			content={$i18n.t('settings.personal.general.parameters.reasoningEffort.label')}
			placement="top"
		>
			<button
				type="button"
				aria-label={$i18n.t('settings.personal.general.parameters.reasoningEffort.label')}
				class="mr-2 flex shrink-0 items-center gap-1 text-[0.8125rem] text-gray-500 transition-colors hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-200"
			>
				<LightBulb className="size-4" />
				<span>{currentLabel}</span>
			</button>
		</Tooltip>
		<div slot="content">
			<DropdownMenu className="w-40 max-w-[calc(100vw-2rem)]">
				{#each EFFORT_OPTIONS as option (option.labelKey)}
					<button
						type="button"
						role="menuitemradio"
						aria-checked={current === option.value}
						class={`${rowClass} ${current === option.value ? selectedClass : ''}`}
						on:click={() => select(option.value)}
					>
						<span class="min-w-0 flex-1 truncate text-left">{$i18n.t(option.labelKey)}</span>
						{#if current === option.value}<Check className="size-3! shrink-0" />{/if}
					</button>
				{/each}
			</DropdownMenu>
		</div>
	</Dropdown>
{/if}
