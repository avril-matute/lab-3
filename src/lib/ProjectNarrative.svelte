<script>
   import Scrolly from "svelte-scrolly";
   let scrollyProgress=0;
   export let projects = {};
   $: sorted_projects=[...projects].sort((a, b)=> a.year-b.year);
   $: progressPerProject=100/(sorted_projects.length);
   $: activeProjectIdx=Math.min(sorted_projects.length-1, Math.floor(scrollyProgress / progressPerProject));
</script>

<!-- svelte-ignore a11y-missing-attribute -->
<div class="scrolly-wrapper">
    <Scrolly bind:progress={ scrollyProgress}>
        {#each sorted_projects as proj}
            <section class=step>
                <div class=step-content>
                    <h3>{proj.title}</h3>
                    <p> {proj.story}</p>
                </div>
            </section>
        {/each}
        <svelte:fragment  slot="viz">
            <div class= "detail">
                {#key activeProjectIdx}
                    <h3>{sorted_projects[activeProjectIdx].year}</h3>
                    <img src={sorted_projects[activeProjectIdx].image} alt={activeProjectIdx.description}>
                {/key}
            </div>
        </svelte:fragment>
    </Scrolly>
</div>

<style>
    :root {
    --color-accent: oklab(67.17699999999999% 0.03447 -0.13406);
    --color-accent-darker: oklab(48.333% 0.02681 -0.10697);
    }

    .scrolly-wrapper{
        width: min (100ch, 60w);
        position: relative;
        left: 50%;
        transform: translateX(-50%);
    }
    .step{
        padding: 2rem;
        min-height: 90vh;

        /* color: gray; */
    }

    .step-content{
        /* color: black */
        border-left: 3px solid var(--color-accent-darker);
        padding: 1.5rem 2rem
    }

    .detail{
        padding: 2rem;
        width: 70%;
        height: 80%;
    }
</style>
