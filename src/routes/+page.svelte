<script>
    import projects from "$lib/projects.json";
    import Project from "$lib/Project.svelte";
    import reading from "$lib/reading.json";
    import ReadingItem from "$lib/ReadingItem.svelte";

    import { onMount } from "svelte";
    // import { base } from '$app/paths';
    // import * as d3 from 'd3';

    // let locData = [];

    // onMount(async () => {
    //     locData = await d3.csv(`${base}/loc.csv`, row => ({
    //         ...row,
    //         line: Number(row.line),
    //         length: Number(row.length),
    //         depth: Number(row.depth)
    //     }));

    //     console.log("My locData:", locData);
    // });

    let githubData = null; // This will eventually hold our Github stats
    let loading = true; // This will be true *until* the fetch's promise resolves to a value
    let error = null; // If the API call resulted in an error, it will go into this variable
    onMount(async () =>{
        //function retrieveGithubData(){
            try{
                console.log("Page has been mounted!!")
                let response = await fetch("https://api.github.com/users/avril-matute");

                console.log(response);
                githubData = await response.json();
                console.log(githubData);
            } catch (err){ //if error, cancel execution and run this code instead
                error=err;
            }
            loading=false;
        //}
    })
    //onMount(retrieveGithubData);

</script>

<title>"Avril Matute: Personal Portfolio"</title>
    <h1> Avril Matute </h1>

<body>

    <img src="images/IMG_5547.JPEG" alt="Me in a museum smiling" width="70%">
    <p> Hi, I am an 11-6 from Honduras :)</p>
    <hr>

    {#if loading}
        <p>Loading...</p>
    {:else if error}
        <p> Something went wrong: {error.message}</p>
    {:else}
        <section>
            <dl>
                <h2> My Github Stats</h2>
                <dt> </dt>
                <dd> </dd>
                <dt> Followers</dt>
                <dd>{githubData.followers}</dd>
                <dt> Following</dt>
                <dd> {githubData.following}</dd>
                <dt> Public Repositories</dt>
                <dd> {githubData.public_repos}</dd>
                <dt> </dt>
                <dd> </dd>
            </dl>
        </section>
    {/if}

    <hr>

    <div class="projectshighlights">
        {#each projects.slice(0, 3) as p}
        <Project data={p} />
        {/each}
    </div>
    <hr>
    <h2> What I'm Reading</h2>
    <br>
    <div class="reading">
        {#each reading.slice(0, 3) as r}
        <ReadingItem book={r} />
        {/each}
    </div>
</body>

<style>

    dl {
        display: grid;
        grid-template-rows: auto 2fr;
        place-items: center left;
        border: 2px solid oklab(67.17699999999999% 0.03447 -0.13406);
    }

    h2{
        grid-row: 1;
        color: oklab(67.17699999999999% 0.03447 -0.13406);
        padding: .5em;
    }

    dt {
        grid-row: 2;
        font-weight: bold;
    }

    dd {
        grid-row: 3;
        padding: 1em;
    }

</style>
