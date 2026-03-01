<script>
    export let data = {};
</script>

<article>
    <h2> {data.title}</h2>
    <img src={data.image} alt="">
    <p>{data.description}</p>
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

</style>
