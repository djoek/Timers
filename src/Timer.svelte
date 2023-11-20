<script>
    import {onMount, onDestroy} from 'svelte';

    export let onClose;

    export let paused = true
    export let autoReset = false
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

    function setTimer() {
        // Changes the default time
        paused = true
        minutes = timerMinutes
        seconds = timerSeconds
        spread(seconds + minutes * 60)

        console.log('set the timer to', minutes, 'minutes and', seconds, 'seconds')
    }

    function initTimer() {
        // creates the interval
        clearInterval(interval);
        setTimeout(() => {  // Synchronized
            interval = setInterval(() => {
                if (!paused) {
                    spread(secondsToGo - 1)
                }
                if (secondsToGo <= 0) {
                    paused = true
                    timerMinutes = 0
                    timerSeconds = 0
                    if (notMuted) {
                        timerAlarm.play();
                    }
                    if (autoReset) {
                        resetTimer();
                    }
                }
            }, 1000)
        }, 1000 - new Date().getMilliseconds());
    }

    function resetTimer() {
        // Sets the timer display back to the default values and clears the interval
        console.log('reset timer')
        spread(seconds + minutes * 60)
    }

    let settings;

    function toggleSettings() {
        settings.show();
    }

    let timerAlarm;
    let notMuted = true;

    onMount(() => {
        spread(secondsToGo)
        initTimer()
        resetTimer()
    });

    onDestroy(() => {
        clearInterval(interval);
    });
</script>


<div class="timer" style="--color1: {color1}; --color2: {color2}; --percentageDone: {percentageDone}">
    <header class="handle">&nbsp;</header>
    <div class="settingsContainer">
        <button on:click={toggleSettings} class="cogField">⚙</button>
        <div bind:textContent={name} class="nameField" contenteditable>{name}</div>
    </div>
    <form class="timerForm"
          on:submit|preventDefault={(ev) => toggleTimer(ev)}
          on:reset|preventDefault={resetTimer}>
        <label>
            <input type="number" inputmode="numeric" bind:value={timerMinutes}
                   on:focus|preventDefault={event => event.target.select()}
                   on:change={setTimer}
                   placeholder={minutes}
                   class="minutesField" disabled={!paused} min="0">
            <span>M</span>
        </label>
        <label>
            <input type="number" inputmode="numeric" bind:value={timerSeconds}
                   on:focus|preventDefault={event => event.target.select()}
                   on:change={setTimer}
                   placeholder={seconds}
                   class="secondsField" disabled={!paused} min="0">
            <span>S</span>
        </label>
        <button type="reset" class="resetField">↺</button>
        <button type="submit">{ paused ? "▶" : "⏸" }</button>
    </form>
    <dialog bind:this={settings}>
        <audio bind:this={timerAlarm} src="/assets/bbbb4x.wav"></audio>

        <div style="display: flex; flex-flow: column; width:16rem;">
            <header><h2>{name} Settings</h2></header>
            <section>
                <label><span>Left Color: </span><input type="color" bind:value={color1}></label>
                <label><span>Right Color: </span><input type="color" bind:value={color2}></label>
                <label><span>Auto Reset: </span><input type="checkbox" bind:checked={autoReset}></label>
                <label><span>Play Sound: </span><input type="checkbox" bind:checked={notMuted}></label>
            </section>
            <footer>
                <form method="dialog">
                    <button>Close</button>
                    <button on:click={onClose} style="background-color:red;">🗑 Delete</button>
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
        background-color: rgba(0, 0, 0, 0.75);
    }

    dialog::backdrop {
        backdrop-filter: blur(5px);
    }

    dialog > div {
        background: linear-gradient(45deg, var(--color1), var(--color2));
        border-radius: 1rem;

        box-shadow: 10px 10px 5px 0px rgba(0, 0, 0, 0.75);
        -webkit-box-shadow: 10px 10px 5px 0px rgba(0, 0, 0, 0.75);
        -moz-box-shadow: 10px 10px 5px 0px rgba(0, 0, 0, 0.75);

        padding: 1rem;
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
        height: 1rem;
        border: 0.1rem solid black;
        outline: none;
        border-radius: 0;
        background-color: rgba(0, 0, 0, 0.33);
        appearance: none;
        vertical-align: bottom;
        text-align: center;
        aspect-ratio: 1;
        display: block;
    }

    dialog > div > footer > form {
        display: flex;
        flex-flow: column nowrap;
        gap: 1rem;
    }

    header.handle {
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
                             "settings settings"
                             "timer timer";
        grid-template-columns: 2fr 12fr;
    }

    form.timerForm {
        grid-area: timer;
        display: grid;
        grid-gap: 0.5rem;
        grid-template-columns: repeat(4, 1fr);
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

    div.settingsContainer {
        grid-area: settings;
        display: grid;
        grid-gap: 0.25rem;
        grid-template-columns: 1fr 8fr;
        color: white;
        background-color: rgba(0, 0, 0, 0);
        font-weight: bold;
        line-height: var(--line-height);
        height: var(--line-height);
        margin: 0.75rem 0.75rem 0.25rem;
    }

    div.settingsContainer > * {
        padding: 0.25rem;
    }

    div.settingsContainer > button {
        border: none;
        background: none;
    }

    form > button[type="reset"] {
        grid-column: span 1;
    }

    form > button[type="submit"] {
        grid-column: span 3;
    }

    div.nameField {
        display: flex;
        color: white;
        font-weight: bold;
        background: linear-gradient(90deg, rgba(0, 0, 0, 0.5) 0 var(--percentageDone), rgba(0, 0, 0, 0.11) var(--percentageDone) 100%);

    }

    form > label {
        grid-column: span 2;
    }

    form > label > input[type="number"] {
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
        background-color: rgba(0, 0, 0, 0);
        appearance: none;
        -webkit-appearance: none;
        -moz-appearance: textfield;
        opacity: 1;

    }

    label > input + span {
        text-align: center;
        margin-left: -1em;
        user-select: none;
        font-size: medium;
        pointer-events: none;
    }
</style>