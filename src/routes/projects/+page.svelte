
<svelte:head>
	<title>Projects</title>
</svelte:head>

<script> 
import * as d3 from 'd3';
import Pie from '$lib/Pie.svelte';
import projects from '$lib/projects.json'; 
import Project from "$lib/Project.svelte"; 

let query = "";

let filteredProjects;
$: filteredProjects = projects.filter(project => {
	let values = Object.values(project).join("\n").toLowerCase();
	return values.includes(query.toLowerCase());
});
let pieData;
$: {let rolledData = d3.rollups(filteredProjects, v => v.length, d => d.year);
    pieData = rolledData.map(([year, count]) => {
        return { value: count, label: year ? year.toString() : 'Unknown' };
    });

}

let selectedYearIndex = -1;
let selectedYear;
$: selectedYear = selectedYearIndex > -1 ? pieData[selectedYearIndex].label : null;

let filteredByYear;
$: filteredByYear = selectedYear ? filteredProjects.filter(project => project.year === selectedYear) : filteredProjects;
</script>

<h1>{projects.length} Projects</h1>

<Pie data={pieData} bind:selectedIndex={selectedYearIndex} />

<input type="search" bind:value={query}
       aria-label="Search projects" placeholder="🔍 Search projects…" />



<div class="projects">
    {#each filteredByYear as p}
       
        <Project info={p} />
    {/each}
   
</div>





