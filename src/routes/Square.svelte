<script lang="ts">
	import { get_twemoji_url } from "./utils";

    export let emoji: string;
    export let selected: boolean;
    export let found: boolean;
</script>

<div class="square" class:flipped={selected}>
    <button on:click />

    <div class="background" />

    {#if !found}
        <img alt={emoji} src={get_twemoji_url(emoji)} />
    {/if}
</div>

<style>
    .square {
        display: flex;
        justify-content: center;
        align-items: center;
        background: yellow;
        transform-style: preserve-3d;
        transition: transform 0.4s;
    }

    .flipped {
        transform: rotateY(180deg);
    }

    button {
        position: absolute;
        width: 100%;
        height: 100%;
        backface-visibility: hidden;
    }

    .background {
        position: absolute;
        background: #eee;
        transform: rotateY(180deg);
        backface-visibility: hidden;
        width: 100%;
        height: 100%;
    }

    img {
        width: 6em;
        height: 6em;
        pointer-events: none;
        transform: rotateY(180deg);
        backface-visibility: hidden;
    }
</style>