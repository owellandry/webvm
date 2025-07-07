<script>
	import { networkData, startLogin, updateButtonData } from "$lib/network.js";
	import { createEventDispatcher } from "svelte";
	import PanelButton from "./PanelButton.svelte";
	import { onMount } from "svelte";
	var dispatch = createEventDispatcher();
	var connectionState = networkData.connectionState;
	var exitNode = networkData.exitNode;
	let connectionType = "";
	let downlink = "";
	let rtt = "";
	let online = navigator.onLine;
	let latency = null;

	function handleConnect() {
		connectionState.set("DOWNLOADING");
		dispatch("connect");
	}

	let buttonData = null;
	$: buttonData = updateButtonData($connectionState, handleConnect);

	function updateConnectionInfo() {
		if ("connection" in navigator) {
			const conn = navigator.connection;
			connectionType = conn.type || conn.effectiveType;
			downlink = conn.downlink;
			rtt = conn.rtt;
		}
		online = navigator.onLine;
	}

	// Medir latencia local (fetch a un recurso local)
	async function testLatency() {
		const start = performance.now();
		// Usamos un recurso local para evitar servicios externos
		try {
			await fetch(window.location.href, {
				method: "HEAD",
				cache: "no-store",
			});
			latency = Math.round(performance.now() - start);
		} catch {
			latency = null;
		}
	}

	onMount(() => {
		updateConnectionInfo();
		testLatency();
		if ("connection" in navigator) {
			navigator.connection.addEventListener(
				"change",
				updateConnectionInfo,
			);
		}
		window.addEventListener("online", updateConnectionInfo);
		window.addEventListener("offline", updateConnectionInfo);
	});
</script>

<h1 class="text-lg font-bold">Networking</h1>
<PanelButton
	buttonImage="assets/tailscale.svg"
	clickUrl={buttonData.clickUrl}
	clickHandler={buttonData.clickHandler}
	rightClickHandler={buttonData.rightClickHandler}
	buttonTooltip={buttonData.buttonTooltip}
	buttonText={buttonData.buttonText}
>
	{#if $connectionState == "CONNECTED"}
		<i
			class="fas fa-circle fa-xs ml-auto {$exitNode
				? 'text-green-500'
				: 'text-amber-500'}"
			title={$exitNode ? "Ready" : "No exit node"}
		></i>
	{/if}
</PanelButton>
<p>WebVM can connect to the Internet via Tailscale</p>
<p>
	Using Tailscale is required since browser do not support TCP/UDP sockets
	(yet!)
</p>

<div class="network-info p-4">
	<h2 class="text-lg font-bold mb-2">Información de Red</h2>
	<ul class="mb-2">
		<li><strong>Tipo de conexión:</strong> {connectionType}</li>
		<li><strong>Velocidad estimada:</strong> {downlink} Mbps</li>
		<li><strong>Latencia estimada (API):</strong> {rtt} ms</li>
		<li><strong>¿En línea?:</strong> {online ? "Sí" : "No"}</li>
		<li>
			<strong>Latencia local (fetch):</strong>
			{latency !== null ? `${latency} ms` : "N/A"}
		</li>
	</ul>
	<button
		class="bg-neutral-700 hover:bg-neutral-500 text-white px-3 py-1 rounded"
		on:click={testLatency}
	>
		Medir latencia local de nuevo
	</button>
</div>

<style>
	.network-info {
		background: rgba(40, 40, 50, 0.85);
		border-radius: 12px;
		color: #fff;
		max-width: 350px;
	}
</style>
