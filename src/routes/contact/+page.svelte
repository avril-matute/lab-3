<svelte:head>
  <title>Contact me</title>
</svelte:head>

<h1> Contact me </h1>

<nav>
    <a href=".">main page</a>
    <a class="current"href="contact"> my contact  </a>
    <a href="projects"> my projects </a>
    <a href="cv"> my CV  </a>
    <a href="https://github.com/avril-matute" target="_blank"> github</a>
</nav>

<form action="mailto:198655851+avril-matute@users.noreply.github.com" method="GET">
    <label> email
        <input name="email" type="email">
    </label>
    <label> subject
        <input name="subject">
    </label>
    <label> message
        <textarea name="body"> </textarea>
    </label>
    <button>submit</button>
</form>
