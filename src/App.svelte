<script>
    import {onMount} from 'svelte';
    import Timer from "./Timer.svelte";
    import '@picocss/pico';
    import dragula from 'dragula';
    import 'dragula/dist/dragula.min.css';

    let main;
    let timers = [];
    let uid = 1;

    $: timers
    $: console.log(timers)
    $: console.log(uid)

    const colors = ["#0e6251", "#0b5345", "#186a3b", "#145a32", "#1b4f72",
        "#154360", "#512e5f", "#4a235a", "#1b2631", "#17202a",
        "#7d6608", "#7e5109", "#784212", "#6e2c00", "#78281f",
        "#641e16", "#7b7d7d", "#626567", "#4d5656", "#424949"]

    timers = [{
        timerId: uid++,
        minutes: 2,
        color1: colors[0],
        color2: colors[5],
    }];

    function addTimer(ev, timerProps) {
        timerProps['timerId'] = uid++
        timers = [...timers, timerProps]
    }

    function pauseAll() {
        timers.forEach(item => item.ref.stopTimer());
    }

    function startAll() {
        timers.forEach(item => item.ref.startTimer());
    }

    // function updatePersistent(timerSettings) {
    //     const timerId = timerSettings['timerId']
    //     timers[timerId] = timerSettings
    //     timers = [...timers]
    // }

    onMount(async () => {
        const drake = dragula([document.querySelector('div.timer'), document.querySelector('.timersContainer')],
            {
                moves: function (el, container, handle) {
                    return handle.classList.contains('handle');
                }
            });

    });

</script>
<header></header>
<main class="container" bind:this={main}>
    <div class="timersContainer">
        {#each timers as timer, i (timer.timerId) }
            <Timer {...timer}
                   bind:this={timer.ref}
                   onClose={() => { timers = timers.toSpliced(i, 1); console.log('delete', i);} }/>
        {/each}
    </div>
    <div class="controls">
        <button on:click={(ev) => addTimer(ev,
        {minutes: 0,
        color1: colors[Math.floor(Math.random() * colors.length)],
        color2: colors[Math.floor(Math.random() * colors.length)],
        })}>+00:00
        </button>
        <button on:click={(ev) => addTimer(ev,
        {minutes: 0, seconds: 10,
        color1: colors[Math.floor(Math.random() * colors.length)],
        color2: colors[Math.floor(Math.random() * colors.length)],
        })}>+00:10
        </button>
        <button on:click={(ev) => addTimer(ev,
        {minutes: 2, seconds: 0,
        color1: colors[Math.floor(Math.random() * colors.length)],
        color2: colors[Math.floor(Math.random() * colors.length)],
        })}>+02:00
        </button>
        <button on:click={(ev) => addTimer(ev,
        {minutes: 5, seconds: 0,
        color1: colors[Math.floor(Math.random() * colors.length)],
        color2: colors[Math.floor(Math.random() * colors.length)],
        })}>+05:00
        </button>
        <button on:click={pauseAll}>⏸ All</button>
        <button on:click={startAll}>⏵ All</button>
    </div>
</main>
<footer>Created in Svelte. Source: <a href="https://github.com/djoek/Timers">GitHub</a></footer>

<style>
    footer {
        background-color: rgba(0, 0, 0, 0.25);
        max-height: 4rem;
        padding: 0.25rem;
        margin: 0;

    }

    main {
        display: flex;
        flex-flow: column;
        gap: 2rem;
        justify-content: center;
        align-items: center;
    }

    main > .timersContainer {
        display: grid;
        grid-template-columns: repeat(auto-fit, 16rem);
        grid-gap: 1rem;
        width: 100vw;
        justify-content: center;
    }

    main > div.controls {
        display: grid;
        grid-gap: 1rem;
        padding: 1rem;
        grid-template-columns: repeat(4, 1fr);
    }

    main > div.controls > button {
        aspect-ratio: 1;
    }

    @media (min-width: 640px) {
        main {
            max-width: none;
        }
    }

</style>