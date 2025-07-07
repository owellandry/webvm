<script>
	export let clickHandler = null;
	export let buttonTooltip = null;
	export let bgColor = "bg-neutral-700";
	export let hoverColor = "hover:bg-neutral-500";
	export let buttonIcon = null;
</script>

<span
	role="button"
	tabindex="0"
	class="inline-block h-7 {bgColor} text-sm p-1 rounded-md shadow-md shadow-neutral-900 {clickHandler !=
	null
		? `${hoverColor} cursor-pointer`
		: ''}"
	title={buttonTooltip}
	on:click={clickHandler}
	on:keydown={(e) => e.key === "Enter" && clickHandler()}
>
	<i class="w-5 {buttonIcon} text-center"></i>
</span>
