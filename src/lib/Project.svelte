<script>
    export let data = {};
</script>

<article>
    <h2>{data.title}</h2>

    {#if data.url}
        <a href={data.url} target="_blank">
            <img src={data.image} alt={data.title}>
        </a>
    {:else}
        <img src={data.image} alt={data.title}>
    {/if}

    <p><b>{data.year} |</b> {data.description}</p>

    {#if data.url}
        <p class="button" color="white"><a href={data.url} target="_blank">View Project &rarr;</a></p>
    {/if}
</article>

<style>
    :root {
    --color-accent: oklab(67.17699999999999% 0.03447 -0.13406);
    --color-accent-darker: oklab(48.333% 0.02681 -0.10697);
    }

    img {
        max-width: 100%;
    }

    .projectshighlights {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(15em, 1fr));
        min-width: 15em;
        max-width: 1fr;
        gap: 1em;
    }

    article {
        grid-template-rows: subgrid;
        grid-row: span 3;
    }

    h2{
        margin: 0
    }

    .button {
        display: inline-block;
        padding: 0.5em 1.2em;
        background-color: var(--color-accent);
        color: white !important;
        text-decoration: none;
        font-weight: bold;
        border-radius: 0.3em;
        transition: background-color 0.2s ease, transform 0.1s ease;
    }

    .button:hover {
        background-color: var(--color-accent-darker);
    }

    .button:active {
        transform: scale(0.95); /* Adds a little "click" animation */
    }

</style>
