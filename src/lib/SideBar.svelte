<script>
	import { createEventDispatcher } from "svelte";
	import Icon from "./Icon.svelte";
	import InformationTab from "./InformationTab.svelte";
	import NetworkingTab from "./NetworkingTab.svelte";
	import CpuTab from "./CpuTab.svelte";
	import DiskTab from "./DiskTab.svelte";
	import AnthropicTab from "./AnthropicTab.svelte";
	import PostsTab from "./PostsTab.svelte";
	import DiscordTab from "./DiscordTab.svelte";
	import GitHubTab from "./GitHubTab.svelte";
	import SmallButton from "./SmallButton.svelte";
	import { cpuActivity, diskActivity, aiActivity } from "./activities.js";
	const icons = [
		{ icon: "fas fa-info-circle", info: "Information", activity: null },
		{ icon: "fas fa-wifi", info: "Networking", activity: null },
		{ icon: "fas fa-microchip", info: "CPU", activity: cpuActivity },
		{ icon: "fas fa-compact-disc", info: "Disk", activity: diskActivity },
		{ icon: "fas fa-robot", info: "ClaudeAI", activity: aiActivity },
		null,
		{ icon: "fas fa-book-open", info: "Posts", activity: null },
		{ icon: "fab fa-discord", info: "Discord", activity: null },
		{ icon: "fab fa-github", info: "GitHub", activity: null },
	];
	let dispatch = createEventDispatcher();
	let activeInfo = null; // Tracks currently visible info.
	let hideTimeout = 0; // Timeout for hiding info panel.
	export let sideBarPinned;

	function showInfo(info) {
		clearTimeout(hideTimeout);
		hideTimeout = 0;
		activeInfo = info;
	}
	function hideInfo() {
		// Never remove the sidebar if pinning is enabled
		if (sideBarPinned) return;
		// Prevents multiple timers and hides the info panel after 400ms unless interrupted.
		clearTimeout(hideTimeout);
		hideTimeout = setTimeout(() => {
			activeInfo = null;
			hideTimeout = 0;
		}, 400);
	}
	function handleMouseEnterPanel() {
		clearTimeout(hideTimeout);
		hideTimeout = 0;
	}
	// Toggles the info panel for the clicked icon.
	function handleClick(icon) {
		if (sideBarPinned) return;
		// Hides the panel if the icon is active. Otherwise, shows the panel with info.
		if (activeInfo === icon.info) {
			activeInfo = null;
		} else {
			activeInfo = icon.info;
		}
	}

	function toggleSidebarPin() {
		sideBarPinned = !sideBarPinned;
		dispatch("sidebarPinChange", sideBarPinned);
	}

	export let handleTool;
</script>

<div class="ubuntu-dock-bottom">
	<div class="ubuntu-dock-icons-horizontal">
		{#each icons as i}
			{#if i}
				<Icon
					icon={i.icon}
					info={i.info}
					activity={i.activity}
					class="ubuntu-dock-icon {activeInfo === i.info
						? 'active'
						: ''}"
					on:mouseover={(e) => showInfo(e.detail)}
					on:click={() => handleClick(i)}
				/>
			{:else}
				<div
					class="grow"
					on:mouseenter={handleMouseEnterPanel}
					role="button"
					tabindex="0"
				></div>
			{/if}
		{/each}
	</div>
	<!-- Panel lateral (ahora modal flotante sobre el dock) -->
	<div
		class="ubuntu-dock-panel {activeInfo ? '' : 'hidden'}"
		on:mouseenter={handleMouseEnterPanel}
		on:mouseleave={hideInfo}
		role="region"
		tabindex="0"
	>
		<div class="absolute right-2 top-2">
			<SmallButton
				buttonIcon="fa-solid fa-thumbtack"
				clickHandler={toggleSidebarPin}
				buttonTooltip={sideBarPinned ? "Unpin Sidebar" : "Pin Sidebar"}
				bgColor={sideBarPinned ? "bg-neutral-500" : "bg-neutral-700"}
			/>
		</div>
		{#if activeInfo === "Information"}
			<InformationTab>
				<slot></slot>
			</InformationTab>
		{:else if activeInfo === "Networking"}
			<NetworkingTab on:connect />
		{:else if activeInfo === "CPU"}
			<CpuTab />
		{:else if activeInfo === "Disk"}
			<DiskTab on:reset />
		{:else if activeInfo === "ClaudeAI"}
			<AnthropicTab {handleTool} />
		{:else if activeInfo === "Posts"}
			<PostsTab />
		{:else if activeInfo === "Discord"}
			<DiscordTab />
		{:else if activeInfo === "GitHub"}
			<GitHubTab />
		{:else}
			<p>TODO: {activeInfo}</p>
		{/if}

		<div class="mt-auto text-sm text-gray-300">
			<div class="pt-1 pb-1">
				<a href="https://cheerpx.io/" target="_blank">
					<span>Powered by CheerpX</span>
					<img
						src="assets/cheerpx.svg"
						alt="CheerpX Logo"
						class="w-6 h-6 inline-block"
					/>
				</a>
			</div>
			<hr class="border-t border-solid border-gray-300" />
			<div class="pt-1 pb-1">
				<a href="https://leaningtech.com/" target="_blank"
					>© 2022-2025 Leaning Technologies</a
				>
			</div>
		</div>
	</div>
</div>

<style>
	.ubuntu-dock-bottom {
		position: fixed;
		left: 0;
		right: 0;
		bottom: 0;
		z-index: 30;
		display: flex;
		flex-direction: column;
		align-items: center;
		pointer-events: none;
	}
	.ubuntu-dock-icons-horizontal {
		display: flex;
		flex-direction: row;
		justify-content: center;
		align-items: flex-end;
		gap: 18px;
		background: rgba(40, 40, 50, 0.7);
		backdrop-filter: blur(8px);
		border-radius: 18px 18px 0 0;
		box-shadow: 0 -2px 12px 0 rgba(0, 0, 0, 0.2);
		padding: 12px 32px 10px 32px;
		margin-bottom: 18px;
		pointer-events: auto;
	}
	.ubuntu-dock-icon {
		width: 54px;
		height: 54px;
		display: flex;
		align-items: center;
		justify-content: center;
		border-radius: 16px;
		margin-bottom: 0;
		background: transparent;
		transition:
			background 0.2s,
			box-shadow 0.2s;
		cursor: pointer;
		border: 2px solid transparent;
	}
	.ubuntu-dock-icon.active,
	.ubuntu-dock-icon:hover {
		background: rgba(244, 116, 33, 0.15);
		box-shadow: 0 0 0 2px #f47421;
		border: 2px solid #f47421;
	}
	.ubuntu-dock-panel {
		position: absolute;
		bottom: 80px;
		left: 50%;
		transform: translateX(-50%);
		min-width: 320px;
		max-width: 400px;
		background: rgba(40, 40, 50, 0.95);
		color: #fff;
		border-radius: 18px;
		box-shadow: 0 4px 32px 0 rgba(0, 0, 0, 0.18);
		padding: 18px 18px 12px 18px;
		transition: opacity 0.2s;
		display: flex;
		flex-direction: column;
		pointer-events: auto;
	}
	.ubuntu-dock-panel.hidden {
		display: none;
	}
</style>
