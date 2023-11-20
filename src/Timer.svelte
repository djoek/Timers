<script>
    import {onMount} from 'svelte';

    export let paused = true
    export let color1 = '#ff0000';
    export let color2 = '#0000ff';
    export let minutes = 0
    export let seconds = 0
    export let timerId = 0
    export let name = "Timer " + timerId

    let interval
    export let secondsToGo = 0

    let timerMinutes = minutes
    let timerSeconds = seconds
    let percentageDone = '0%'

    $: secondsToGo = parseInt(timerMinutes) * 60 + parseInt(timerSeconds)
    $: percentageDone = Number(1 - (secondsToGo / (parseInt(minutes) * 60 + parseInt(seconds)))).toLocaleString(undefined, {
        style: 'percent',
        minimumFractionDigits: 0
    });

    function spread(stg) {
        timerMinutes = String(Math.floor(stg / 60)).padStart(2, '0');
        timerSeconds = String(stg - Math.floor(stg / 60) * 60).padStart(2, '0');
        return stg
    }

    function toggleTimer(ev) {
        if (paused) {
            ev.target.focus()
            spread(secondsToGo)


        }
        paused = !paused
    }

    function resetTimer() {
        paused = true
        clearInterval(interval);
        minutes = timerMinutes
        seconds = timerSeconds

        setTimeout(() => {  // Synchronized
            interval = setInterval(() => {
                if (!paused) {
                    spread(secondsToGo - 1)
                }
                if (secondsToGo <= 0) {
                    clearInterval(interval);
                    timerMinutes = 0
                    timerSeconds = 0
                }
            }, 1000)
        }, 1000 - new Date().getMilliseconds());
    }

    let settings;

    function toggleSettings() {
        settings.show();
    }

    onMount(() => {
        spread(secondsToGo)
        resetTimer()
    });
</script>


<div class="timer" style="--color1: {color1}; --color2: {color2}; --percentageDone: {percentageDone}">
    <div class="handle">&nbsp;</div>
    <div on:click={toggleSettings} class="settings">⚙</div>
    <div bind:textContent={name} class="nameField" contenteditable>{name}</div>
    <form class="timerForm"
          on:submit|preventDefault={(ev) => toggleTimer(ev)}
          on:reset|preventDefault={resetTimer}>
        <label>
            <input type="number" inputmode="numeric" bind:value={timerMinutes}
                   on:focus|preventDefault={event => event.target.select()}
                   class="minutesField" disabled={!paused} min="0">
            <span>M</span>
        </label>
        <label>
            <input type="number" inputmode="numeric" bind:value={timerSeconds}
                   on:focus|preventDefault={event => event.target.select()}
                   class="secondsField" disabled={!paused} min="0">
            <span>S</span>
        </label>

        <button type="reset">SET</button>
        <button type="submit">{ paused ? "▶" : "⏸" }</button>
    </form>
    <dialog bind:this={settings}>
        <div style="display: flex; flex-flow: column; width:16rem;">
            <header><h2>{name} Settings</h2></header>
            <section>
                <label><span>Left Color: </span><input type="color" bind:value={color1}></label>
                <label><span>Right Color: </span><input type="color" bind:value={color2}></label>
                <label><span>Timer Default: </span><input type="number" bind:value={minutes}><input type="number"
                                                                                                    bind:value={seconds}></label>
            </section>
            <footer>
                <form method="dialog">
                    <button autofocus>Close</button>
                </form>
            </footer>
        </div>
    </dialog>
</div>


<style>
    dialog {
        grid-gap: 0.5rem;
        grid-template-columns: 2fr 1fr;
        flex-flow: column;
        background-color: rgba(0, 0, 0, 0.66);
    }

    dialog > div > section {
        display: flex;
        flex-flow: column nowrap;
        gap: 0.5rem;
    }

    dialog > div > section > label {
        display: grid;
        grid-template-columns: 4fr 1fr 1fr;
        justify-content: center;
        text-align: left;
        height: 2rem;
    }

    dialog > div > section > label > input {
        margin: 0;
        padding: 0;
        font-size: medium;
        height: 2rem;
        display: inline;
        border: none;
        outline: none;
        border-radius: 0;
        background-color: rgba(0, 0, 0, 0.33);
        appearance: none;
        vertical-align: bottom;
        text-align: center;
    }

    div.handle {
        grid-area: handle;
        border-radius: 0 0 0.25rem 0.25rem;
        background: repeating-linear-gradient(
                -45deg,
                rgba(0, 0, 0, 0.10),
                rgba(0, 0, 0, 0.10) 0.5rem,
                rgba(0, 0, 0, 0) 0.5rem,
                rgba(0, 0, 0, 0) 1rem
        );
    }

    div.timer {

        background: linear-gradient(45deg, var(--color1), var(--color2));
        border-radius: 1rem;

        box-shadow: 10px 10px 5px 0px rgba(0, 0, 0, 0.75);
        -webkit-box-shadow: 10px 10px 5px 0px rgba(0, 0, 0, 0.75);
        -moz-box-shadow: 10px 10px 5px 0px rgba(0, 0, 0, 0.75);

        display: grid;
        grid-template-areas: "handle handle"
                             "settings name"
                             "timer timer";
        grid-template-columns: 2fr 12fr;
    }

    form.timerForm {
        grid-area: timer;
        display: grid;
        grid-gap: 0.5rem;
        grid-template-columns: 1fr 1fr;
        margin: 0.75rem;
    }

    label {
        text-align: right;
        display: grid;
        grid-template-columns: 5fr 0fr;
        font-family: monospace, sans-serif;
        margin: 0;
        padding: 0;
    }


    button {
        margin: 0;
        background-color: rgba(0, 0, 0, 0.25);
        border: 0.25rem solid rgba(0, 0, 0, 1);
        border-radius: 0.5rem;

    }

    button:active {
        font-weight: bolder;
    }

    button[type=submit] {
    }

    div.nameField {
        grid-area: name;
        display: flex;
        color: white;
        font-weight: bold;
        line-height: var(--line-height);
        height: var(--line-height);
        margin-right: 0.75rem;
        margin-top: 0.75rem;
        padding: 0.25rem;
        background: linear-gradient(90deg, rgba(0, 0, 0, 0.5) 0 var(--percentageDone), rgba(0, 0, 0, 0.11) var(--percentageDone) 100%);

    }

    div.settings {
        grid-area: settings;
        display: flex;
        color: white;
        background-color: rgba(0, 0, 0, 0);
        font-weight: bold;
        line-height: var(--line-height);
        height: var(--line-height);
        justify-content: center;
        align-items: center;
        margin-left: 0.75rem;
        margin-top: 0.75rem;
    }


    form input[type="number"] {
        display: inline;
        border: none;
        outline: none;
        border-radius: 0;
        background-color: rgba(0, 0, 0, 0.33);
        margin: 0;
        padding: 0;
        appearance: none;
        vertical-align: bottom;
        font-size: xxx-large;
        text-align: center;
        font-weight: bold;

    }


    form input[type="number"]:disabled {
        color: white;
        background-color: rgba(0, 0, 0, 1);
        appearance: none;
        opacity: 1;

    }

    label > input + span {
        text-align: center;
        margin-left: -1em;
        user-select: none;
        font-size: medium;
        z-index: -1
    }
</style>