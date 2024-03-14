
<svelte:head>
	<title>Home</title>
</svelte:head>

<script> import projects from '$lib/projects.json'; 
    import Project from "$lib/Project.svelte";
    
    let profileData = {
	ok: true,
	json: async () => ({
		followers: 100,
		following: 100,
		public_repos: 100,
		public_gists: 100,
	})
};

</script>

<h1>Carlos <b>Mariano</b> Salcedo <b>Salazar</b></h1>
<p>Welcome! I am a senior at MIT interested in AI and Ethics</p>
<img class= "home_image" src="images/ai_pic.png" alt="Description of the image">



{#await fetch("https://api.github.com/users/MARIANO2024") }
	<p>Loading...</p>
{:then response}
	{#await response.json()}
		<p>Decoding...</p>
	{:then data}
		<p>The data is {data.followers}</p>
        <section>
            <h2>My GitHub Stats</h2>
            <dl class="grid-container">
                <dt>Followers:</dt>
                <dd>{data.followers}</dd>
              
                <dt>Following</dt>
                <dd>{data.following}</dd>
              
                <dt>Public Repos:</dt>
                <dd>{data.public_repos}</dd>
              </dl>
        </section>

	{:catch error}
		<p class="error">
			Something went wrong: {error.message}
		</p>
	{/await}
{:catch error}
	<p class="error">
		Something went wrong: {error.message}
	</p>
{/await}



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