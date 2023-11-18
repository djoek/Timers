<script>
    import {onMount} from 'svelte';
    import Timer from "./Timer.svelte";
    import '@picocss/pico';
    import dragula from 'dragula';
    import 'dragula/dist/dragula.min.css';

    onMount(async () => {
         const drake = dragula([document.querySelector('div.timer'), document.querySelector('main.container')], {});
    });

</script>

<main class="container">
    <Timer name="Bob" color="Orange" />
    <Timer name="Alice" color="Gold" />
    <Timer name="Trudy" color="Pink" />
    <Timer name="Jose" color="Purple" />
    <Timer name="John" color="Indigo" />
    <Timer name="Paul" color="DarkViolet" />
    <Timer name="George" color="SeaGreen" />
    <Timer name="Ringo" color="DimGray" />

    <Timer name="Larry" color="SteelBlue" />
    <Timer name="Moe" color="Maroon" />
    <Timer name="Curly" color="Brown" />
    <Timer name="Homer" color="Olive" class="timer"/>
    <Timer name="Marge" color="Navy" class="timer"/>
    <Timer name="Bart" color="Teal" class="timer"/>
    <Timer name="Lisa" color="DarkGray" class="timer"/>
    <Timer name="Alanis" color="Red" class="timer"/>
</main>

<style>

    main {
        gap: 1rem;
        display: flex;
        flex-flow: row wrap;
        flex-grow: 1;
        justify-content: center;
    }

    @media (min-width: 640px) {
        main {
            max-width: none;
        }
    }
</style>