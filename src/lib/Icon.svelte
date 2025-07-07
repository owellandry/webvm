<script>
	export let icon;
	export let info;
	export let activity;
	import { createEventDispatcher } from "svelte";

	const dispatch = createEventDispatcher();
	function handleMouseover() {
		dispatch("mouseover", info);
	}
	function handleClick() {
		dispatch("click", { icon, info });
	}
</script>

<div
	class="p-3 cursor-pointer text-center hover:bg-[#772953] {$activity
		? 'text-orange-500 animate-pulse'
		: 'hover:text-gray-100'}"
	style="animation-duration: 0.5s"
	on:mouseenter={handleMouseover}
	on:click={handleClick}
	on:keydown={(e) => e.key === "Enter" && handleClick()}
	role="button"
	tabindex="0"
>
	<i class="{icon} fa-xl"></i>
</div>
