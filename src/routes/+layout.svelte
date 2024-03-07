

<script>
   
import { page } from '$app/stores';
// { JSON.stringify($page) };

let pages = [
	{url: "./", title: "Home"},
	{url: "./projects", title: "Projects"},
    {url: "./resume", title: "Resume"},
	{url: "./contact", title: "Contact"},
    {url: "https://github.com/MARIANO2024", title: "GitHub"}
	
];



</script> 

<nav>
	{#each pages as p }
        <a href={ p.url } class:current={ "." + $page.route.id === p.url} target={ p.url.startsWith("http") ? "_blank" : null }>{ p.title } </a>
	{/each}
</nav>


<slot />