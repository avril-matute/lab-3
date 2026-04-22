<script>
  import projects from "$lib/projects.json";
  import Project from "$lib/Project.svelte";
  import ProjectNarrative from "$lib/ProjectNarrative.svelte";
  let years = projects.map(proj => proj.year);
  let range = Math.max(...years)-Math.min(...years);
  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  import Bar from '$lib/Bar.svelte';

  let rawData = [];
  let wrangled = [];
  let totalLines = 0; // Optional: declare this if you want to use it in the template too

  onMount(async () => {
      rawData = await d3.json('/lab6_example.json');

      // 1. Compute the total number of lines across all rows
      totalLines = d3.sum(rawData, d => d.lines);

      // 2. Rollup data, returning both the line count and the percentage
      wrangled = d3.rollups(
          rawData,
          v => {
              const sum = d3.sum(v, d => d.lines);
              return {
                  lines: sum,
                  percentage: (sum / totalLines) * 100
              };
          },
          d => d.language
      );
  });
  $: barData = d3.rollups(projects, v => v.length, d => d.year)
    .map(([year, count]) => ({ label: String(year), value: count }));

</script>

<svelte:head>
  <title>Projects</title>
</svelte:head>

  <!-- <section>
      <h2>Data wrangling result</h2>
      <pre>{JSON.stringify(wrangled, null, 2)}</pre>
  </section> -->

<Bar data={barData} />


<h1> { projects.length } Projects Over {range} Years</h1>

<p>Scroll down to see a timeline of my projects and crafty hobbies throughout my life.</p>
<div>
  <ProjectNarrative {projects}/>
</div>
<br>
<i class="outro"> Thanks for scrolling through my project story!
 Feel free to explore all of the projects at your leisure below.</i>
<br>
<br>
<br>
<div class="projects">
  {#each projects as p}
    <Project data={p} />
  {/each}
</div>
