
<svelte:head>
	<title>Home</title>
</svelte:head>

<script> import projects from '$lib/projects.json'; 
    import Project from "$lib/Project.svelte"; </script>

<h1>Carlos <b>Mariano</b> Salcedo <b>Salazar</b></h1>
<p>Welcome! I am a senior at MIT interested in AI and Ethics</p>
<img class= "home_image" src="images/ai_pic.png" alt="Description of the image">

<h2>Latest Work</h2>
<div class="projects">

    {#each projects.slice(0,4) as p}
        <!-- <article>
            <h2>{p.title}</h2>
            <img src={p.image} alt="">
            <p>{p.description}</p>
        </article> -->
        <Project info={p} hLevel=3/>
    {/each}
</div>
