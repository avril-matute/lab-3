<script>
    export let book = {};
</script>

<article>
    <h3> {book.title}</h3>
    <i>{book.author}</i>
    <br>
    <img src={book.image} alt="">
</article>

<style>
    :root {
    --color-accent: oklab(67.17699999999999% 0.03447 -0.13406);
    --color-accent-darker: oklab(48.333% 0.02681 -0.10697);
    }

    body {
        /* Set line height to 1.5 times the font size and use the
        OI's UI font as the website font */
        accent-color: var(--color-accent);
        font: 100%/1.5 system-ui;
        max-width: 300ch;
        margin-inline: auto;
        padding: 5ch;
    }


    img {
        width: 15em;
        padding: 1em;
        margin-inline: auto;

    }

    #reading {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(15em, 1fr));
        min-width: 15em;
        max-width: 1fr;
        gap: 1em;
    }

    article {
        grid-template-rows: subgrid;
        grid-row: span 1;
    }

    h3{
        line-height: 1.1;
        margin: 0;
        color: var(--color-accent-darker)
    }


</style>
