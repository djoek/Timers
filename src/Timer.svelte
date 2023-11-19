<script>
    export let paused = true
    export let color1 = '#ff0000';
    export let color2 = '#0000ff';
    export let secondsToGo = 0
    export let name = ""
    export let timerId = 0

    let interval

    $: timerMinutes = Math.floor(secondsToGo / 60)
    $: timerSeconds = secondsToGo - Math.floor(secondsToGo / 60) * 60
    $: secondsToGo

    function startTimer(ev) {
        ev.target.blur()
        stopTimer()
        secondsToGo = timerMinutes * 60 + timerSeconds
        paused = false
        interval = setInterval(() => {
            if (paused) {
                return
            }
            secondsToGo = secondsToGo - 1
            if (secondsToGo <= 0) {
                stopTimer()
                secondsToGo = 0
                paused = true
            }
        }, 1000);
    }

    function pauseTimer() {
        paused = !paused
        if (secondsToGo <= 0) {
            paused = true
        }
    }

    function stopTimer() {
        clearInterval(interval);
    }

    let settings;

    function toggleSettings() {
        settings.show();
    }

</script>


<div class="timer" style="--color1: {color1}; --color2: {color2};">
    <div class="handle">&nbsp;</div>
    <div on:click={toggleSettings} class="settings">⚙</div>
    <div bind:textContent={name} class="nameField" contenteditable>{name}</div>
    <form on:submit|preventDefault={(ev) => startTimer(ev)} class="timerForm">
        <label>
            <input type="text" inputmode="numeric" bind:value={timerMinutes} on:focus={event => event.target.select()}
                   class="minutesField">
            <span>M</span>
        </label>
        <label>
            <input type="text" inputmode="numeric" bind:value={timerSeconds} on:focus={event => event.target.select()}
                   class="secondsField">
            <span>S</span>
        </label>
        <button type="button" on:click={pauseTimer}>{ paused ? "▶" : "⏸" }</button>
        <button type="submit">SET</button>
    </form>
    <dialog bind:this={settings}>
        <header>Settings</header>
        <section>
            <input type="color" bind:value={color1}>
            <input type="color" bind:value={color2}>
        </section>
        <footer>
            <form method="dialog">
                <button>Close</button>
            </form>
        </footer>
    </dialog>
</div>


<style>
    dialog {
        grid-gap: 0.5rem;
        grid-template-columns: 1fr 1fr;
        flex-flow: column;
        background-color: rgba(0,0,0,0.66);

    }

    dialog > input {
        width: 4rem;
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

        background: linear-gradient(-45deg, var(--color1), var(--color2));
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

    form {
        grid-area: timer;
        display: grid;
        grid-gap: 0.5rem;
        grid-template-columns: 1fr 1fr;
        margin: 0.75rem;
    }

    label {
        font-size: xx-large;
        text-align: right;
        display: flex;
        flex-flow: row;

    }

    button {
        margin: 0;
        background-color: rgba(0, 0, 0, 0.66);
        border: none;

    }

    button:active {
        font-weight: bolder;

    }

    button[type=submit] {
    }

    button[type=button] {
    }



    input[type="text"] {

        text-align: right;
        font-size: xx-large;
        border: none;
        outline: none;
        border-radius: 0;
        background-color: rgba(0, 0, 0, 0.33);
        margin: 0;
        padding-top: 0;
        padding-bottom: 0;
        padding-right: 2rem;
        appearance: none;

    }

    label > input + span {
        margin-left: -1rem;
        user-select: none;
        font-size: small;
        vertical-align: middle;
        display: block;
    }


    div.nameField {
        grid-area: name;
        display: flex;
        color: white;
        background-color: rgba(0, 0, 0, 0.11);
        font-weight: bold;
        line-height: var(--line-height);
        height: var(--line-height);
        margin-right: 0.75rem;
        margin-top: 0.75rem;
        padding: 0.25rem;
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

</style>