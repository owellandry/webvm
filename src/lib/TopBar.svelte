<script>
  import { onMount } from "svelte";
  let time = "";
  let interval;

  // Estado para mostrar/ocultar el panel de red
  let showNetwork = false;
  let connectionType = "";
  let downlink = "";
  let rtt = "";
  let online = navigator.onLine;
  let latency = null;

  function updateTime() {
    const now = new Date();
    time = now.toLocaleTimeString([], { hour: "2-digit", minute: "2-digit" });
  }

  function updateConnectionInfo() {
    if ("connection" in navigator) {
      const conn = navigator.connection;
      connectionType = conn.type || conn.effectiveType;
      downlink = conn.downlink;
      rtt = conn.rtt;
    }
    online = navigator.onLine;
  }

  async function testLatency() {
    const start = performance.now();
    try {
      await fetch(window.location.href, { method: "HEAD", cache: "no-store" });
      latency = Math.round(performance.now() - start);
    } catch {
      latency = null;
    }
  }

  function getWifiLevel() {
    if (!online) return "none";
    if (downlink >= 20) return "full"; // Excelente
    if (downlink >= 5) return "medium"; // Media
    if (downlink > 0) return "low"; // Baja
    return "none";
  }

  onMount(() => {
    updateTime();
    interval = setInterval(updateTime, 1000);
    updateConnectionInfo();
    testLatency();
    if ("connection" in navigator) {
      navigator.connection.addEventListener("change", updateConnectionInfo);
    }
    window.addEventListener("online", updateConnectionInfo);
    window.addEventListener("offline", updateConnectionInfo);
    return () => clearInterval(interval);
  });
</script>

<div class="ubuntu-topbar">
  <div class="ubuntu-topbar-left">
    <span class="ubuntu-logo">●</span>
    <span class="ubuntu-title">WebVM</span>
  </div>
  <div class="ubuntu-topbar-center">
    <span class="ubuntu-time">{time}</span>
  </div>
  <div class="ubuntu-topbar-right">
    <span
      class="ubuntu-topbar-icon"
      title="Network"
      on:click={() => (showNetwork = !showNetwork)}
    >
      {#if getWifiLevel() === "full"}
        <!-- WiFi full -->
        <svg width="20" height="20" viewBox="0 0 20 20">
          <circle cx="10" cy="10" r="8" fill="#fff" opacity="0.2" />
          <path
            d="M5 13a7 7 0 0 1 10 0"
            stroke="#fff"
            stroke-width="2"
            fill="none"
          />
          <path
            d="M7.5 15.5a3.5 3.5 0 0 1 5 0"
            stroke="#fff"
            stroke-width="2"
            fill="none"
          />
          <circle cx="10" cy="17" r="1.2" fill="#fff" />
        </svg>
      {:else if getWifiLevel() === "medium"}
        <!-- WiFi media -->
        <svg width="20" height="20" viewBox="0 0 20 20">
          <circle cx="10" cy="10" r="8" fill="#fff" opacity="0.2" />
          <path
            d="M7.5 15.5a3.5 3.5 0 0 1 5 0"
            stroke="#fff"
            stroke-width="2"
            fill="none"
          />
          <circle cx="10" cy="17" r="1.2" fill="#fff" />
        </svg>
      {:else if getWifiLevel() === "low"}
        <!-- WiFi baja -->
        <svg width="20" height="20" viewBox="0 0 20 20">
          <circle cx="10" cy="10" r="8" fill="#fff" opacity="0.2" />
          <circle cx="10" cy="17" r="1.2" fill="#fff" />
        </svg>
      {:else}
        <!-- Sin conexión: WiFi con X -->
        <svg width="20" height="20" viewBox="0 0 20 20">
          <circle cx="10" cy="10" r="8" fill="#fff" opacity="0.2" />
          <path
            d="M5 13a7 7 0 0 1 10 0"
            stroke="#fff"
            stroke-width="2"
            fill="none"
          />
          <path
            d="M7.5 15.5a3.5 3.5 0 0 1 5 0"
            stroke="#fff"
            stroke-width="2"
            fill="none"
          />
          <circle cx="10" cy="17" r="1.2" fill="#fff" />
          <line x1="6" y1="6" x2="14" y2="14" stroke="#f00" stroke-width="2" />
          <line x1="14" y1="6" x2="6" y2="14" stroke="#f00" stroke-width="2" />
        </svg>
      {/if}
      {#if showNetwork}
        <div class="network-dropdown">
          <h2 class="text-base font-bold mb-1">Red</h2>
          <ul class="mb-1">
            <li><strong>Tipo:</strong> {connectionType}</li>
            <li><strong>Velocidad:</strong> {downlink} Mbps</li>
            <li><strong>Latencia (API):</strong> {rtt} ms</li>
            <li><strong>¿En línea?:</strong> {online ? "Sí" : "No"}</li>
            <li>
              <strong>Latencia local:</strong>
              {latency !== null ? `${latency} ms` : "N/A"}
            </li>
          </ul>
          <button class="network-btn" on:click|stopPropagation={testLatency}
            >Medir latencia</button
          >
        </div>
      {/if}
    </span>
    <span class="ubuntu-topbar-icon" title="Volume">
      <svg width="20" height="20" viewBox="0 0 20 20"
        ><rect
          x="3"
          y="8"
          width="4"
          height="4"
          rx="1"
          fill="#fff"
          opacity="0.2"
        /><rect x="3" y="8" width="4" height="4" rx="1" fill="#fff" /><path
          d="M10 8v4l4 2V6l-4 2z"
          fill="#fff"
        /></svg
      >
    </span>
    <span class="ubuntu-topbar-user">Usuario</span>
    <span class="ubuntu-topbar-icon" title="Power">
      <svg width="20" height="20" viewBox="0 0 20 20"
        ><circle cx="10" cy="10" r="8" fill="#fff" opacity="0.2" /><path
          d="M10 5v5"
          stroke="#fff"
          stroke-width="2"
        /><path
          d="M6 13a4 4 0 0 0 8 0"
          stroke="#fff"
          stroke-width="2"
          fill="none"
        /></svg
      >
    </span>
  </div>
</div>

<style>
  .ubuntu-topbar {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 38px;
    background: rgba(40, 40, 50, 0.85);
    color: #fff;
    display: flex;
    align-items: center;
    z-index: 100;
    box-shadow: 0 2px 8px 0 rgba(0, 0, 0, 0.08);
    font-family: "Ubuntu", Arial, sans-serif;
  }
  .ubuntu-topbar-left {
    display: flex;
    align-items: center;
    gap: 10px;
    margin-left: 18px;
  }
  .ubuntu-logo {
    color: #f47421;
    font-size: 1.5em;
    font-weight: bold;
    margin-right: 6px;
  }
  .ubuntu-title {
    font-size: 1.1em;
    font-weight: 700;
    letter-spacing: 1px;
  }
  .ubuntu-topbar-center {
    flex: 1;
    text-align: center;
    font-size: 1.1em;
    font-weight: 500;
  }
  .ubuntu-time {
    background: rgba(255, 255, 255, 0.08);
    border-radius: 8px;
    padding: 2px 12px;
  }
  .ubuntu-topbar-right {
    display: flex;
    align-items: center;
    gap: 18px;
    margin-right: 18px;
  }
  .ubuntu-topbar-user {
    background: rgba(255, 255, 255, 0.08);
    border-radius: 8px;
    padding: 2px 10px;
    font-size: 1em;
    font-weight: 500;
  }
  .ubuntu-topbar-icon {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 28px;
    height: 28px;
    border-radius: 50%;
    background: rgba(255, 255, 255, 0.08);
    transition: background 0.2s;
    cursor: pointer;
    position: relative;
  }
  .ubuntu-topbar-icon:hover {
    background: #f47421;
  }
  .network-dropdown {
    position: absolute;
    top: 36px;
    right: 0;
    background: rgba(40, 40, 50, 0.97);
    color: #fff;
    border-radius: 10px;
    box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.18);
    padding: 12px 18px 10px 18px;
    min-width: 220px;
    z-index: 200;
    font-size: 0.98em;
  }
  .network-btn {
    background: #444;
    color: #fff;
    border: none;
    border-radius: 6px;
    padding: 4px 12px;
    margin-top: 6px;
    cursor: pointer;
    transition: background 0.2s;
  }
  .network-btn:hover {
    background: #f47421;
  }
</style>
