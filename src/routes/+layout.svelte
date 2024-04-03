

<script>

let root = globalThis?.document?.documentElement;
$: root?.style.setProperty("color-scheme", colorScheme);

let localStorage = globalThis.localStorage ?? {};
let colorScheme = localStorage.colorScheme ?? "light dark";
$: localStorage.colorScheme = colorScheme;

import { page } from '$app/stores';
// { JSON.stringify($page) };

let pages = [
	{url: "./", title: "Home"},
	{url: "./projects", title: "Projects"},
    {url: "./resume", title: "Resume"},
	{url: "./contact", title: "Contact"},
    {url: "https://github.com/MARIANO2024", title: "GitHub"},
	{url:"./meta", title: "Meta"}
];


</script> 

<nav>
	{#each pages as p }
        <a href={ p.url } class:current={ "." + $page.route.id === p.url} target={ p.url.startsWith("http") ? "_blank" : null }>{ p.title } </a>
	{/each}
</nav>

<style>


    select,input,textarea,button{
    font-family: inherit;
    }

</style>

<label class="color-scheme">
    Theme:
    <select bind:value={ colorScheme }>
    <option value="dark">Dark</option>
    <option value="light dark">Light Dark</option>
    <option value="light">Light</option>
    </select>
</label>



<slot />