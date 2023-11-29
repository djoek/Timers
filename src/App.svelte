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
<header>
    <span style="inline-size: max-content; word-wrap: none; ">S P A C E T I M E R S</span>
        <a on:click|preventDefault={pauseAll} href="">⏸ All</a>
        <a on:click|preventDefault={startAll} href="">⏵ All</a>
</header>
<main bind:this={main}>
    <div class="timersContainer container">
        {#each timers as timer, i (timer.timerId) }
            <Timer {...timer}
                   bind:this={timer.ref}
                   onDelete={() => { timers = timers.toSpliced(i, 1); console.log('delete', i);} }/>
        {/each}
        <div class="addATimer" style="">
            <button class="plusButton" on:click|preventDefault={(ev) => addTimer(ev,
        {minutes: 0, seconds: 0,
        color1: colors[Math.floor(Math.random() * colors.length)],
        color2: colors[Math.floor(Math.random() * colors.length)],
        })}>+
            </button>
        </div>
    </div>
</main>
<footer>
    <span><a href="https://github.com/djoek/Timers">GitHub</a></span>

</footer>

<style>
    @media (min-width: 1600px) {
        .container {
            max-width: 1530px;
        }
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
        grid-gap: 2rem;
        padding: 0;
        margin-bottom: 0;
        grid-template-columns: repeat(auto-fit, 16rem);
        justify-content: center;
        align-items: center;
    }

    header, footer {
        font-family: monospace;
        background-color: rgba(0, 0, 0, 0.25);
        max-height: 4rem;
        padding: 0.25rem 1rem;
        margin: 0;
        display: flex;
        flex-flow: row nowrap;
        gap: 1rem;
        align-items: center;

    }

    footer {
        justify-content: end;
    }

    div.addATimer {
        border: thick dashed rgba(255, 255, 255, 0.25);
        height: 100%;
        border-radius: 1rem;
        display: flex;
        margin: 0;
    }

    button.plusButton {
        border: 0;
        background: none;
        color: rgba(255, 255, 255, 0.25);
        font-size: 8rem;
        font-weight: bolder;
        height: 100%;
    }

    button.plusButton:active, button.plusButton:focus {
        appearance: none;
        outline: none;
        border-color: inherit;
        -webkit-box-shadow: none;
        box-shadow: none;
    }

    /*div.controls > button {*/
    /*    display: block;*/
    /*    width: 8rem;*/
    /*    margin: 0;*/
    /*    padding: 0;*/
    /*}*/

    @media (min-width: 640px) {
        main {
            max-width: none;
        }
    }


</style>