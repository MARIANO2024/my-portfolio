<svelte:head>
	<title>Meta</title>
</svelte:head>

<script>    
    import * as d3 from "d3";
    import { onMount } from "svelte";

    let data = []; 
    let commits = [];
    

    onMount(async () => {
	    data = await d3.csv("loc.csv", row => ({
        ...row,
        line: Number(row.line), // or just +row.line
        depth: Number(row.depth),
        length: Number(row.length),
        date: new Date(row.date + "T00:00" + row.timezone),
        datetime: new Date(row.datetime)
        }));

        commits = d3.groups(data, d => d.commit).map(([commit, lines]) => {
        let first = lines[0];
        let {author, date, time, timezone, datetime} = first;
        let ret = {
            id: commit,
            url: "https://github.com/vis-society/lab-7/commit/" + commit,
            author, date, time, timezone, datetime,
            hourFrac: datetime.getHours() + datetime.getMinutes() / 60,
            totalLines: lines.length
        };

        // Like ret.lines = lines
        // but non-enumerable so it doesn’t show up in JSON.stringify
        Object.defineProperty(ret, "lines", {
            value: lines,
            configurable: true,
            writable: true,
            enumerable: false,
        });

        // let width = 1000;
        // let height = 600;
        
        // const yScale= d3.scaleLinear().domain(0,24).range(0,height);
        // const xScale= d2.scaleTime().domain(0,d3.max(ret.date)).range(0,width);

        return ret;

        
    
    });

        
    });

    let width = 1000;
    let height = 600;
        

    
    const yScale= d3.scaleLinear().domain([0,24]).range([0,height]);
    let xScale;
    $: xScale= d3.scaleTime().domain([d3.min(data, d=> d.date),d3.max(data, d=> d.date)]).range([0,width]);
    
</script>

<header>
    <h1>Some stats about the code in this website, hence Meta</h1>
</header>

<dl class="stats">
	<dt>Total Lines of Code</dt>
	<dd>{data.length}</dd>

    <dt>Max Depth</dt>
	<dd>{d3.max(data, d => d.depth)}</dd>

    <dt>Mean Depth</dt>
	<dd>{d3.mean(data, d => d.depth)}</dd>

    <dt>Number of Files</dt>
    <dd>{d3.group(data, d => d.file).size}</dd>
</dl>


<svg viewBox="0 0 {width} {height}">

    <g class="dots">
        {#each commits as commit, index }
            <circle
                cx={ xScale(commit.datetime) }
                cy={ yScale(commit.hourFrac) }
                r="5"
                fill="steelblue"
            />
        {/each}
    </g>

</svg>

<style>
	svg {
		overflow: visible;
	}
</style>

