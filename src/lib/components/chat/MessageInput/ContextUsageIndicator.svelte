<script lang="ts">
	import Tooltip from '$lib/components/common/Tooltip.svelte';

	// 已占用窗口的百分比（可超过 100，圆环按 100 截断）
	export let usedPercent = 0;
	// 悬停提示文案，由调用方按当前语言拼好
	export let label = '';

	const RING_RADIUS = 7;
	const RING_CIRCUMFERENCE = 2 * Math.PI * RING_RADIUS;
	// 用量达到该百分比后圆环改为警示色
	const WARNING_PERCENT = 80;

	$: ringPercent = Math.min(100, Math.max(0, usedPercent));
	$: ringOffset = RING_CIRCUMFERENCE * (1 - ringPercent / 100);
	$: ringColorClass =
		ringPercent >= WARNING_PERCENT ? 'text-amber-500' : 'text-gray-500 dark:text-gray-400';
</script>

<Tooltip content={label} placement="top">
	<div class="mr-2 flex shrink-0 items-center self-center" role="img" aria-label={label}>
		<svg viewBox="0 0 20 20" class="size-4 -rotate-90 {ringColorClass}" fill="none">
			<circle
				cx="10"
				cy="10"
				r={RING_RADIUS}
				stroke="currentColor"
				stroke-width="2.5"
				class="opacity-25"
			/>
			<circle
				cx="10"
				cy="10"
				r={RING_RADIUS}
				stroke="currentColor"
				stroke-width="2.5"
				stroke-linecap="round"
				stroke-dasharray={RING_CIRCUMFERENCE}
				stroke-dashoffset={ringOffset}
			/>
		</svg>
	</div>
</Tooltip>
