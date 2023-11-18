<script>
    import {onMount} from 'svelte';
    import Timer from "./Timer.svelte";
    import '@picocss/pico';
    import dragula from 'dragula';
    import 'dragula/dist/dragula.min.css';

    onMount(async () => {
        const drake = dragula([document.querySelector('div.timer'), document.querySelector('main.container')],
            {
                moves: function (el, container, handle) {
                    return handle.classList.contains('handle');
                }
            })
    });

</script>

<main class="container">
    <Timer timerId=1 name="Bob" color="Orange"/>
    <Timer timerId=2 name="Alice" color="Gold"/>
    <Timer timerId=3 name="Trudy" color="Pink"/>
    <Timer timerId=4 name="Jose" color="Purple"/>
    <Timer timerId=5 name="John" color="Indigo"/>
    <Timer timerId=6 name="Paul" color="DarkViolet"/>
    <Timer timerId=7 name="George" color="SeaGreen"/>
    <Timer timerId=8 name="Ringo" color="DimGray"/>

    <Timer timerId=9 name="Larry" color="SteelBlue"/>
    <Timer timerId=10 name="Moe" color="Maroon"/>
    <Timer timerId=11 name="Curly" color="Brown"/>
    <Timer timerId=12 name="Homer" color="Olive"/>
    <Timer timerId=13 name="Marge" color="Navy"/>
    <Timer timerId=14 name="Bart" color="Teal"/>
    <Timer timerId=15 name="Lisa" color="DarkGray"/>
    <Timer timerId=16 name="Alanis" color="Red"/>
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