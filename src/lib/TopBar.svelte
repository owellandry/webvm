<script>
import { onMount } from 'svelte';
let time = '';
let interval;

function updateTime() {
  const now = new Date();
  time = now.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
}

onMount(() => {
  updateTime();
  interval = setInterval(updateTime, 1000);
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
    <span class="ubuntu-topbar-icon" title="Network">
      <svg width="20" height="20" viewBox="0 0 20 20"><circle cx="10" cy="10" r="8" fill="#fff" opacity="0.2"/><path d="M5 13a7 7 0 0 1 10 0" stroke="#fff" stroke-width="2" fill="none"/><path d="M7.5 15.5a3.5 3.5 0 0 1 5 0" stroke="#fff" stroke-width="2" fill="none"/></svg>
    </span>
    <span class="ubuntu-topbar-icon" title="Volume">
      <svg width="20" height="20" viewBox="0 0 20 20"><rect x="3" y="8" width="4" height="4" rx="1" fill="#fff" opacity="0.2"/><rect x="3" y="8" width="4" height="4" rx="1" fill="#fff"/><path d="M10 8v4l4 2V6l-4 2z" fill="#fff"/></svg>
    </span>
    <span class="ubuntu-topbar-user">Usuario</span>
    <span class="ubuntu-topbar-icon" title="Power">
      <svg width="20" height="20" viewBox="0 0 20 20"><circle cx="10" cy="10" r="8" fill="#fff" opacity="0.2"/><path d="M10 5v5" stroke="#fff" stroke-width="2"/><path d="M6 13a4 4 0 0 0 8 0" stroke="#fff" stroke-width="2" fill="none"/></svg>
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
  box-shadow: 0 2px 8px 0 rgba(0,0,0,0.08);
  font-family: 'Ubuntu', Arial, sans-serif;
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
  background: rgba(255,255,255,0.08);
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
  background: rgba(255,255,255,0.08);
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
  background: rgba(255,255,255,0.08);
  transition: background 0.2s;
  cursor: pointer;
}
.ubuntu-topbar-icon:hover {
  background: #f47421;
}
</style>
