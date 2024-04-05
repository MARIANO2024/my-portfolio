
<script>

    import * as d3 from 'd3';


    let arcGenerator = d3.arc().innerRadius(20).outerRadius(50);
    let colors = d3.scaleOrdinal(d3.schemeTableau10);

    export let data = [];

    let arcData;
    $: arcData= d3.pie().value(d=>d.value)(data);

    let arcs;
    $: arcs=arcData.map(d => arcGenerator(d)); 

    export let selectedIndex =  -1;

    function toggleWedge (index, event) {
	if (!event.key || event.key === "Enter") {
		selectedIndex = selectedIndex === index ? -1 : index;
	}

}
// on:click={e => selectedIndex = selectedIndex === i ? -1 : i} />
</script>

<div class="container">
    <svg viewBox="-50 -50 100 100">
        {#each arcs as arc,i}
            <path tabindex="0" role="button" aria-label="Wedge" d={arc} fill={colors(i)}
            class:selected={selectedIndex === i}
            on:click={e => toggleWedge(i, e)} 
            on:keyup={e => toggleWedge(i, e)}/>
        {/each}
    </svg> 

    <ul class="legend">
        {#each data as d, index}
            <li style="--color: { colors(index) }" class:selected={selectedIndex === index}>
                <span class="swatch"></span>
                {d.label} <em>({d.value})</em>
            </li>
        {/each}
    </ul>
</div>

<style>

    svg {
        max-width: 20em;
        margin-block: 2em; 
    
        /* Do not clip shapes outside the viewBox */
        overflow: visible;
      
    }
</style>
    

