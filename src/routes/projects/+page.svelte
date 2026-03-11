<script>
  import projects from "$lib/projects.json";
  import Project from "$lib/Project.svelte";
  import ProjectNarrative from "$lib/ProjectNarrative.svelte";
  let years = projects.map(proj => proj.year);
  let range = Math.max(...years)-Math.min(...years);

</script>

<svelte:head>
  <title>Projects</title>
</svelte:head>

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
