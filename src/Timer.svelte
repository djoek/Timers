<script>
    import {onMount, onDestroy} from 'svelte';

    export let onClose;
    export let updatePersistent;

    export let timerId = 0
    export let name = "Timer " + timerId
    export let autoReset = false
    export let color1 = '#ff0000';
    export let color2 = '#0000ff';
    export let minutes = 0
    export let seconds = 0

    function endDate(secondsDelta) {
        return new Date(Date.now() + secondsDelta * 1000)
    }

    let timerEnds = endDate(parseInt(minutes) * 60 + parseInt(seconds))

    function getConfig() {
        return {
            timerId: timerId,
            name: name,
            autoReset: autoReset,
            color1: color1,
            color2: color2,
            minutes: minutes,
            seconds: seconds,
        }
    }

    let interval
    export let secondsToGo = 0

    let timerMinutes = minutes
    let timerSeconds = seconds
    let percentageDone = '0%'

    $: secondsToGo = Math.floor((timerEnds - new Date()) / 1000)
    $: percentageDone = Number(1 - (secondsToGo / (parseInt(minutes) * 60 + parseInt(seconds)))).toLocaleString(
        undefined, {
            style: 'percent',
            minimumFractionDigits: 0
        });

    $: stopped = interval === undefined

    function spread(stg) {
        timerMinutes = String(Math.floor(stg / 60)).padStart(2, '0');
        timerSeconds = String(stg - Math.floor(stg / 60) * 60).padStart(2, '0');
        return stg
    }

    export function startTimer() {
        if (!stopped) {
            stopTimer()
        }
        const updateDelta = 250
        timerEnds = endDate(parseInt(timerMinutes) * 60 + parseInt(timerSeconds))
        setTimeout(() => {  // Synchronized to the clock second
            interval = setInterval(() => {
                secondsToGo = Math.floor((timerEnds - new Date()) / 1000)
                spread(secondsToGo)
                if (secondsToGo <= 0) {
                    timerMinutes = 0
                    timerSeconds = 0
                    stopTimer()
                    spread(0)
                    if (notMuted) {
                        timerAlarm.play();
                    }
                    if (autoReset) {
                        resetTimer();
                    }
                }
            }, updateDelta)
        }, 1000 - new Date().getMilliseconds());
    }

    export function stopTimer() {
        clearInterval(interval);
        interval = undefined
    }

    function toggleTimer(ev) {
        ev.target.focus()
        if (interval) {
            stopTimer()
        } else {
            startTimer()
        }

    }

    function setTimer() {
        // Changes the default time
        minutes = parseInt(timerMinutes) || 0
        seconds = parseInt(timerSeconds) || 0
        spread(seconds + minutes * 60)
        timerEnds = endDate(parseInt(timerMinutes) * 60 + parseInt(timerSeconds))
        console.log('set the timer to', minutes, 'minutes and', seconds, 'seconds')
    }

    function resetTimer() {
        // Sets the timer display back to the default values and clears the interval
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
        resetTimer()
    });

    onDestroy(() => {
        clearInterval(interval);
    });
</script>

<div class="singleTimerContainer" style="--color1: {color1}; --color2: {color2}; --percentageDone: {percentageDone}">
    <dialog bind:this={settings}>
        <audio bind:this={timerAlarm} src="/assets/bbbb4x.wav"></audio>
        <div style="display: flex; flex-flow: column; gap:1rem; width:16rem;">
            <header><h2>settings</h2></header>
            <section>
                <label>
                    <span>Colors</span>
                    <input type="color" bind:value={color1}>
                    <span></span>
                    <input type="color" bind:value={color2}>
                </label>
                <label>
                    <span>Auto Reset: </span><input type="checkbox" bind:checked={autoReset}>
                    <span>Play Sound: </span><input type="checkbox" bind:checked={notMuted}>
                </label>
            </section>
            <footer>
                <form method="dialog">
                    <button autofocus>Close</button>
                    <button on:click={onClose} style="background-color:red;">🗑</button>
                </form>
            </footer>
        </div>
    </dialog>

    <div class="timer">
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
                       on:input={setTimer}
                       placeholder={minutes}
                       class="minutesField" disabled={!stopped} min="0">
                <span>M</span>
            </label>
            <label>
                <input type="number" inputmode="numeric" bind:value={timerSeconds}
                       on:focus|preventDefault={event => event.target.select()}
                       on:input={setTimer}
                       placeholder={seconds}
                       class="secondsField" disabled={!stopped} min="0">
                <span>S</span>
            </label>
            <button type="reset" class="resetField" disabled={!stopped}>↺</button>
            <button type="submit">{ interval ? "⏸" : "▶" }</button>

        </form>

    </div>

</div>
<style>
    dialog {
        min-width: 16rem;
        min-height: 14rem;
        padding: 0;
        margin: 0;
        position: absolute;
        top: inherit;
        left: inherit;
        bottom: inherit;
        right: inherit;
        background-color: rgba(0, 0, 0, 0);
        border-radius: 1rem;
        font-family: 'Orbitron', serif;
    }

    dialog::backdrop {
        backdrop-filter: none;
    }

    dialog > div {

        background: linear-gradient(-45deg, var(--color1), var(--color2));
        border-radius: 1rem;
        padding: 1rem;
        width: 16rem;
        height: 14rem;
        box-shadow: 0 0.5rem 0 2px rgba(0, 0, 0, 0.5), 0 0.5rem 0 1px var(--color1);
        font-size: medium;

    }

    dialog > div > section {
        display: flex;
        flex-flow: column nowrap;
        gap: 0;
        padding: 0.5rem;
        margin: 0;
        box-shadow: inset 1px 1px 1px 1px rgba(0, 0, 0, 0.33);

    }

    dialog header, dialog footer, h2 {
        margin: 0;
        font-family: 'Orbitron', serif;
        font-size: large;
    }

    dialog > div > section > label {
        display: grid;
        grid-template-columns: repeat(4, 1fr);
        justify-content: center;
        text-align: left;
    }

    dialog > div > section label > span, dialog > div > section > label > input {
        display: flex;
        align-items: center;
        height: 2rem;
        width: 2rem;
        padding: 0.1rem;
    }

    dialog > div > section > label > input {
        display: flex;
        background-color: rgba(0, 0, 0, 0.25);
    }


    dialog > div > footer > form {
        display: flex;
        flex-flow: row nowrap;
        gap: 1rem;
        margin: 0;
    }

    dialog > div > footer > form button {
        height: 2rem
    }

    header.handle {
        grid-area: handle;
        border-radius: 1rem 1rem 0 0;
        background: repeating-linear-gradient(
                -45deg,
                rgba(0, 0, 0, 0.10),
                rgba(0, 0, 0, 0.10) 0.5rem,
                rgba(0, 0, 0, 0) 0.5rem,
                rgba(0, 0, 0, 0) 1rem
        );
    }

    div.timer {
        font-family: 'Orbitron', sans-serif;

        background: linear-gradient(45deg, var(--color1), var(--color2));
        border-radius: 1rem;
        width: 16rem;
        height: 14rem;
        box-shadow: 0 0.5rem 0 2px rgba(0, 0, 0, 0.5), 0 0.5rem 0 1px var(--color1);


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
        font-weight: bolder;
        font-size: x-large;
        margin: 0;
        background-color: rgba(0, 0, 0, 0.01);
        border: 1px solid rgba(0, 0, 0, 0.15);
        border-radius: 0.5rem;
        box-shadow: 0 4px 0 1px rgba(0, 0, 0, 0.49);
        transform: translate(0, -4px);
        transition-property: transform, box-shadow;
        transition-duration: 4ms;
        font-family: monospace;

        display: flex;
        justify-content: center;
        align-items: center;
    }

    button:active {
        box-shadow: 0 1px 0 0 rgba(0, 0, 0, 0.49);
        transform: translate(0, 0);
        transition-property: transform, box-shadow;
        transition-duration: 4ms;
    }

    button:disabled {
        box-shadow: 0 1px 0 0 rgba(0, 0, 0, 0.49);
        transform: translate(0, 0);
        transition-property: transform, box-shadow;
        transition-duration: 4ms;
    }

    button[type=submit] {
    }

    button.cogField {
        aspect-ratio: 1;
        width: 2rem;
    }

    div.settingsContainer {
        grid-area: settings;
        display: grid;
        grid-gap: 0.33rem;
        grid-template-columns: 1fr 8fr;
        color: white;
        background-color: rgba(0, 0, 0, 0);
        font-weight: bold;
        margin: 0.75rem 0.75rem 0.25rem;
        align-items: center;
    }

    div.settingsContainer > * {
        padding: 0.25rem;
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
        box-shadow: inset 1px 1px 1px 1px rgba(0, 0, 0, 0.33);

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
        font-family: 'Orbitron', sans-serif;

        box-shadow: inset 1px 1px 1px 1px rgba(0, 0, 0, 0.33);
        text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.33);
    }


    form input[type="number"]:disabled {
        color: white;
        background-color: rgba(0, 0, 0, 0);
        appearance: none;
        -webkit-appearance: none;
        -moz-appearance: textfield;
        opacity: 1;

    }

    form.timerForm label > input + span {
        text-align: center;
        margin-left: -1em;
        user-select: none;
        font-size: medium;
        pointer-events: none;
    }
</style>