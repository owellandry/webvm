<script>
	export let title;
	export let image;
	export let url;
</script>

<a href={url} target="_blank"
	><div class="bg-neutral-700 hover:bg-neutral-500 p-2 rounded-md">
		<img class="w-full aspect-[40/21]" src={image} alt={title} />
		<h2 class="text-sm font-bold">{title}</h2>
	</div></a
>
