<script>
    export let paused = true
    export let color = '#' + Math.floor(Math.random() * 16777215).toString(16);
    export let secondsToGo = 0
    export let name = ""

    let interval

    $: timerMinutes = Math.floor(secondsToGo / 60)
    $: timerSeconds = secondsToGo - Math.floor(secondsToGo / 60) * 60

    function startTimer() {
        secondsToGo = timerMinutes * 60 + timerSeconds
        paused = false
        interval = setInterval(() => {
            if (paused) {
                return
            }
            secondsToGo = secondsToGo - 1
            if (secondsToGo < 0) {
                stopTimer()
                secondsToGo = 0
                paused = true
            }
        }, 1000);
    }

    function pauseTimer() {
        paused = !paused
    }

    function stopTimer() {
        clearInterval(interval);
    }


</script>


<div class="timer" style="--theme-color: {color}">
    <div bind:textContent={name} class="nameField" contenteditable>{name}</div>
    <form on:submit|preventDefault={startTimer} class="timerForm">
        <label>
            <input type="text" bind:value={timerMinutes} on:focus={event => event.target.select()} class="minutesField">
            <span>M</span>
        </label>
        <label>
            <input type="text" bind:value={timerSeconds} on:focus={event => event.target.select()} class="secondsField">
            <span>S</span>
        </label>
        <button type="button" on:click={pauseTimer}>{ paused ? "▶" : "⏸" }</button>
        <button type="submit">RESET</button>
    </form>
</div>


<style>
    div.timer {
        width: 15rem;
        height: 12rem;
        background-color: var(--theme-color);
        padding: 1rem;
        border-radius: 1rem;
    }

    form {
        display: grid;
        grid-gap: 0.5rem;
        grid-template-columns: 1fr 1fr;
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
        color: white;
        text-align: center;
        background-color: rgba(0, 0, 0, 0);
        font-weight: bold;
        line-height: var(--line-height);
        height: var(--line-height);
        padding: 0.5rem;
    }


</style>