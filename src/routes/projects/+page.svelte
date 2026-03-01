<script>
  import projects from "$lib/projects.json";
  import Project from "$lib/Project.svelte";
</script>

<svelte:head>
  <title>Projects</title>
</svelte:head>

<h1> { projects.length } Projects </h1>
<nav>
    <a href=".">main page</a>
    <a href="contact"> my contact  </a>
    <a class="current" href="projects"> my projects </a>
    <a href="cv"> my CV  </a>
    <a href="https://github.com/avril-matute" target="_blank"> github</a>
</nav>

<div class="projects">
  {#each projects as p}
    <Project data={p} />
  {/each}
</div>
