<script>
    import Timer from "./Timer.svelte";
    import '@picocss/pico';

</script>

<main class="grid">
    <Timer name="Bob" color="#333333"/>
    <Timer name="Alice" color="#643173"/>
    <Timer name="Trudy" color="#23395B"/>
    <Timer name="Jose" color="#7E007B"/>
</main>

<style>
    main {
    }

    @media (min-width: 640px) {
        main {
            max-width: none;
        }
    }
</style>