<script>
    import { onMount } from "svelte";
    export let title = "Terminal";
    let x = 120;
    let y = 80;
    let dragging = false;
    let offsetX = 0;
    let offsetY = 0;
    let windowRef;
    let removeListeners = () => {};

    function onMouseDown(e) {
        dragging = true;
        offsetX = e.clientX - x;
        offsetY = e.clientY - y;
        document.body.style.userSelect = "none";
    }

    function onMouseMove(e) {
        if (dragging) {
            // Limitar los valores de x e y para que la ventana no se salga de la pantalla
            const winWidth = window.innerWidth;
            const winHeight = window.innerHeight;
            const termWidth = windowRef ? windowRef.offsetWidth : 900;
            const termHeight = windowRef ? windowRef.offsetHeight : 540;
            let newX = e.clientX - offsetX;
            let newY = e.clientY - offsetY;
            // Limitar a los bordes de la pantalla
            newX = Math.max(0, Math.min(newX, winWidth - termWidth));
            newY = Math.max(0, Math.min(newY, winHeight - termHeight));
            x = newX;
            y = newY;
        }
    }

    function onMouseUp() {
        dragging = false;
        document.body.style.userSelect = "";
    }

    // Bloque reactivo para listeners globales de mouse
    $: if (typeof window !== "undefined" && dragging) {
        window.addEventListener("mousemove", onMouseMove);
        window.addEventListener("mouseup", onMouseUp);
    } else if (typeof window !== "undefined") {
        window.removeEventListener("mousemove", onMouseMove);
        window.removeEventListener("mouseup", onMouseUp);
    }
</script>

<div
    bind:this={windowRef}
    class="ubuntu-window"
    style="left: {x}px; top: {y}px;"
>
    <div
        class="ubuntu-window-titlebar"
        role="button"
        tabindex="0"
        on:mousedown={onMouseDown}
        style="cursor: grab;"
    >
        <div class="ubuntu-window-buttons">
            <span class="ubuntu-window-btn close" title="Cerrar"></span>
            <span class="ubuntu-window-btn minimize" title="Minimizar"></span>
            <span class="ubuntu-window-btn maximize" title="Maximizar"></span>
        </div>
        <span class="ubuntu-window-title">{title}</span>
    </div>
    <div class="ubuntu-window-content">
        <div class="ubuntu-window-slot-wrapper">
            <slot></slot>
        </div>
    </div>
</div>

<style>
    .ubuntu-window {
        position: absolute;
        width: 900px;
        height: 540px;
        background: rgba(30, 30, 40, 0.98);
        border-radius: 16px;
        box-shadow: 0 8px 48px 0 rgba(0, 0, 0, 0.28);
        display: flex;
        flex-direction: column;
        overflow: hidden;
        border: 1.5px solid #3c224d;
        z-index: 50;
        transition: box-shadow 0.2s;
    }
    .ubuntu-window:active {
        box-shadow: 0 12px 64px 0 rgba(0, 0, 0, 0.38);
    }
    .ubuntu-window-titlebar {
        height: 38px;
        background: linear-gradient(90deg, #77216f 0%, #f47421 100%);
        display: flex;
        align-items: center;
        padding: 0 18px;
        user-select: none;
    }
    .ubuntu-window-buttons {
        display: flex;
        gap: 8px;
        margin-right: 18px;
    }
    .ubuntu-window-btn {
        width: 14px;
        height: 14px;
        border-radius: 50%;
        display: inline-block;
        margin-top: 2px;
        border: 1.5px solid #fff2;
    }
    .ubuntu-window-btn.close {
        background: #e95420;
        border-color: #e95420;
    }
    .ubuntu-window-btn.minimize {
        background: #f7c325;
        border-color: #f7c325;
    }
    .ubuntu-window-btn.maximize {
        background: #3fa23f;
        border-color: #3fa23f;
    }
    .ubuntu-window-title {
        font-weight: 500;
        color: #fff;
        font-size: 1.08em;
        letter-spacing: 0.5px;
    }
    .ubuntu-window-content {
        flex: 1;
        padding: 0;
        background: transparent;
        display: flex;
        flex-direction: column;
        width: 100%;
        height: 100%;
    }
    .ubuntu-window-slot-wrapper {
        flex: 1;
        width: 100%;
        height: 100%;
        display: flex;
    }
</style>
