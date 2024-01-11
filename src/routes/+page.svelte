<script lang="ts">
    import Game from './Game.svelte';
    import '../styles.css';
    import Modal from './Modal.svelte';
	import { levels } from './levels';
    import { confetti } from '@neoconfetti/svelte';

    let state: 'waiting' | 'playing' | 'paused' | 'won' | 'lost' = 'waiting';

    let game: Game;
</script>

<Game 
    bind:this={game} 
    on:play={() => {
        state = 'playing';
    }}
    on:pause={() => {
        state = 'paused';
    }}
    on:win={() => {
        state = 'won';
    }}
    on:lose={() => {
        state = 'lost';
    }}
/>

{#if state !== 'playing'}
    <Modal>
        <header>
            <h1>Memo<span>Ji</span></h1>
            <p>the emoji matching game</p>
        </header>

        {#if state === 'won' || state === 'lost'}
            <p>you {state} the game! play again?</p>
        {:else if state === 'paused'}
            <p>game paused</p>
        {:else if state === 'waiting'}
            <p>choose a level</p>
        {/if}

        <div class="buttons">
            {#if state === 'paused'}
                <button on:click={() => game.resume()}><p>resume</p></button>
                <button on:click={() => (state = 'waiting')}><p>quit</p></button>
            {:else}
                {#each levels as level}
                    <button on:click={() => {
                        game.start(level);
                        state = 'playing';
                    }}><p>{level.label}</p></button>
                {/each}
            {/if}
        </div>
    </Modal>
{/if}

{#if state === 'won'}
    <div 
        class="confetti" 
        use:confetti={{
            stageWidth: innerWidth,
            stageHeight: innerHeight
        }} 
    />
{/if}

<style>
    h1 {
        font-size: 4em;
    }

    h1 span {
        color: orangered;
    }

    p {
        font-family: 'Press Start 2P', sans-serif;
        display: flex;
        justify-content: center;
        text-align: center;
    }

    .buttons {
		display: flex;
		justify-content: center;
		gap: 0.5em;
	}

    .confetti {
        position: fixed;
        top: 30%;
        left: 50%;
        width: 100%;
        height: 100%;
        pointer-events: none;
    }

    header {
        text-align: center;
        margin-inline: 0.5em;
    }
    
</style>