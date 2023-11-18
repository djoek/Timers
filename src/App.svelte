<script>
    import Timer from "./Timer.svelte";
    import '@picocss/pico';

</script>

<main class="container">
    <Timer name="Bob" color="#333333"/>
    <Timer name="Alice" color="#643173"/>
    <Timer name="Trudy" color="#23395B"/>
    <Timer name="Jose" color="#7E007B"/>
</main>

<style>
    main {
        gap: 1rem;
        display: flex;
        flex-flow: row wrap;
        flex-grow: 1;
        background-color: rgba(0,0,0,0.33);
          justify-content: center;
    }
    @media (min-width: 640px) {
        main {
            max-width: none;
        }
    }
</style>