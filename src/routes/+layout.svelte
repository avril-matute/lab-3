<script>
    import "../style.css";
    import { page } from "$app/stores";
    import { base } from "$app/paths";
    let localStorage = globalThis.localStorage ?? {};
    let pages = [
    {url: "/", title: "about me"},
    {url: "/projects", title: "my projects"},
    {url: "/cv", title: "my cv"},
    {url: "/contact", title: "my contact"},
    {url: "https://github.com/avril-matute", title: "github"},
    // add other pages here
    ];
    $: root?.style.setProperty("color-scheme", colorScheme);
    let colorScheme = "light dark";
    $: localStorage.colorScheme = colorScheme;
    let root = globalThis.document?.documentElement;
    if (localStorage.colorScheme) { // if localStorage has a colorScheme property
        colorScheme = localStorage.colorScheme; // override the default colorScheme
    }
</script>

<label class="color-scheme-switch">
    Theme:
    <select bind:value={ colorScheme }>
        <option value="light dark">Automatic</option>
        <option value="light">Light</option>
        <option value="dark">Dark</option>
    </select>
</label>

<nav>
    <!-- You read a conditional operator as condition ? result if true : result if false.
    Here, we check if p.url starts with the string "http", and if it does, we set target="_blank".
    If p.url does not start with "http", we set target=null. -->
    {#each pages as p}
        <a href={base+p.url}
            class:current={p.url === "/" // is this link the home page?
            ? $page.url.pathname === (base + "/") // if yes - set current = true if the path name matches.
             // else, set current = true if the path name starts correctly
            : $page.url.pathname.startsWith(base + p.url)}
            target={p.url.startsWith("http") ? "_blank" : null}
        >
            {p.title}
        </a>
    {/each}
</nav>

<slot/> <!-- renders page conents -->

<style>
    :root {
        border-bottom-color: oklch(43.291% 0.06124 304.309 / 0.4);
    }

    nav {
        accent-color: var(--color-accent);
        text-align: center;
        display: flex;
        margin-bottom: 2em;
        --border-color: oklch(50% 10% 200 / 40%);
        border-bottom: 2px solid var(--border-color);
    }

    nav a {
        flex: 1; /* step 2.2 for each element to take the same space  */
        text-decoration: none; /* Remove the underline from the links by setting */
        color: inherit;
        text-align: center;
        padding: .5em;
    }

    nav a.current {
        border-bottom-width: .4em;
        border-bottom-style: solid;
        border-bottom-color: oklch(80% 3% 200);
        padding: .2em;
        border-bottom: 4px solid var(--border-color);
    }

    nav a:hover {
        border-bottom-width: .4em;
        border-bottom-color: var(--color-accent);
        border-bottom-style: solid;
        padding: .2em;
        background-color: oklab(67.17699999999999% 0.03447 -0.13406 / 0.274);
    }

    .color-scheme-switch {
        position: absolute;
        top: 1rem;
        right: 1rem;
        display: inline-flex;
        gap: 4px;
        font-size: 80%;
        font-family: inherit;
    }
</style>
