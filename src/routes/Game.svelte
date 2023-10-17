<script lang="ts">
	import { createEventDispatcher, onMount } from "svelte";
	import Countdown from "./Countdown.svelte";
    import Found from "./Found.svelte";
    import Grid from "./Grid.svelte";
    import type { Level } from "./levels";
	import { shuffle } from "./utils";

    const dispatch = createEventDispatcher();

    let size: number;
    let grid: string[] = [];
    let found: string[] = [];
    let remaining: number;
    let duration: number;
    let playing: boolean;

    export function start(level: Level) {
        size = level.size;
        remaining = duration = level.duration;
        grid = create_grid(level);
        found = [];

        resume();
    }

    export function resume() {
        playing = true;
        countdown();

        dispatch('play');
    }

    function create_grid(level: Level) {
        const copy = level.emojis.slice();
        const pairs: string[] = [];

        for (let i = 0; i< level.size ** 2 / 2; i += 1) {
            const index = Math.floor(Math.random() * copy.length);
            const emoji = copy[index];
            
            copy.splice(index, 1);
            pairs.push(emoji);
        }

        pairs.push(...pairs);

        return shuffle(pairs);
    }

    function countdown() {
        const start = Date.now();
        const remaining_at_start = remaining;

        function loop() {
            if (!playing) return;

            requestAnimationFrame(loop);

            remaining = remaining_at_start - (Date.now() - start);

            if (remaining <= 0) {
                playing = false;
                dispatch('lose');
            }
        }

        loop();
    }
</script>

<div class="game" style="--size: {size}">
    <div class="info">
        {#if playing}
            <Countdown 
                {remaining} 
                duration={duration} 
                on:click={() => {
                    playing = false;
                    dispatch('pause');
                }}
            />
        {/if}
    </div>

    <div class="grid-container">
        <Grid 
            {grid} 
            on:found={(e) => {
                found = [...found, e.detail.emoji];

                if (found.length === size ** 2 / 2) {
                    playing = false;
                    setTimeout(() => {
                        playing = false;
                        dispatch('win');
                    }, 1000);
                }
            }}
            {found}
        />
    </div>

    <div class="info">
        <Found {found} />
    </div>
</div>

<style>
    .game {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        height: 100%;
        font-size: min(1vmin, 0.5rem);
    }

    .info {
        width: 80em;
        height: 10em;
    }

    .grid-container {
        width: 80em;
        height: 80em;
    }
</style>