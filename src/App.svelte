<script>
    import Timer from "./Timer.svelte";
    import '@picocss/pico';

</script>

<main class="container">
    <Timer name="Bob" color="Orange"/>
    <Timer name="Alice" color="Gold"/>
    <Timer name="Trudy" color="Pink"/>
    <Timer name="Jose" color="Purple"/>
    <Timer name="John" color="Indigo"/>
    <Timer name="Paul" color="DarkViolet"/>
    <Timer name="George" color="SeaGreen"/>
    <Timer name="Ringo" color="DimGray"/>

    <Timer name="Larry" color="SteelBlue"/>
    <Timer name="Moe" color="Maroon"/>
    <Timer name="Curly" color="Brown"/>
    <Timer name="Homer" color="Olive"/>
    <Timer name="Marge" color="Navy"/>
    <Timer name="Bart" color="Teal"/>
    <Timer name="Lisa" color="DarkGray"/>
    <Timer name="Alanis" color="Red"/>
</main>

<style>

    main {
        gap: 1rem;
        display: flex;
        flex-flow: row wrap;
        flex-grow: 1;
        background-color: rgba(0, 0, 0, 0.33);
        justify-content: center;
    }

    @media (min-width: 640px) {
        main {
            max-width: none;
        }
    }
</style>