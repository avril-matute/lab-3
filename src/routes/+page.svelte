<script>
  import projects from "$lib/projects.json";
  import Project from "$lib/Project.svelte";
  import reading from "$lib/reading.json";
  import ReadingItem from "$lib/ReadingItem.svelte";
</script>

<title>"Avril Matute: Personal Portfolio"</title>
    <h1> Avril Matute </h1>
<nav>
    <a class="current" href=".">main page</a>
    <a href="contact"> my contact  </a>
    <a href="projects"> my projects </a>
    <a href="cv"> my CV  </a>
    <a href="https://github.com/avril-matute" target="_blank"> github</a>
</nav>

<body>

    <img src="images/IMG_5547.JPEG" alt="Me in a museum smiling" width="70%">
    <p> Hi, I am an 11-6 from Honduras :)</p>
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
