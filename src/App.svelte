<script>
    import {onMount} from 'svelte';
    import Timer from "./Timer.svelte";
    import '@picocss/pico';
    import dragula from 'dragula';
    import 'dragula/dist/dragula.min.css';

    const colors = ["#0e6251", "#0b5345", "#186a3b", "#145a32", "#1b4f72",
                    "#154360", "#512e5f", "#4a235a", "#1b2631", "#17202a",
                    "#7d6608", "#7e5109", "#784212", "#6e2c00", "#78281f",
                    "#641e16", "#7b7d7d", "#626567", "#4d5656", "#424949"]

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
	{#each Array(15) as _, index (index)}
        <Timer timerId={index+1}
               minutes=5 seconds=0
               color1={colors[index]}
               color2={colors[Math.floor(Math.random()*colors.length)]}/>
    {/each}
</main>

<style>

    main {
        display: grid;
        grid-template-columns: repeat(auto-fit, 16rem);
        grid-gap: 1rem;
        justify-content: center;

    }

    @media (min-width: 640px) {
        main {
            max-width: none;
        }
    }

</style>