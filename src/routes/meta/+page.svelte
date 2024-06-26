<svelte:head>
	<title>Meta</title>
</svelte:head>

<script>    
    import * as d3 from "d3";
    import { onMount } from "svelte";

    import Pie from "$lib/Pie.svelte";

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
    
    
    
    let margin = {top: 10, right: 10, bottom: 30, left: 40};

    let usableArea = {
        top: margin.top,
        right: width - margin.right,
        bottom: height - margin.bottom,
        left: margin.left
    };
    usableArea.width = usableArea.right - usableArea.left;
    usableArea.height = usableArea.bottom - usableArea.top;
    
    let xScale,yScale;
    $: {
    xScale= d3.scaleTime().domain([d3.min(data, d=> d.datetime),d3.max(data, d=> d.datetime)]).range([usableArea.left,usableArea.right]).nice();
    yScale= d3.scaleLinear().domain([0,24]).range([usableArea.bottom,usableArea.top]);
    }

    let xAxis, yAxis;
    $: {
	d3.select(xAxis).call(d3.axisBottom(xScale));
	// d3.select(yAxis).call(d3.axisLeft(yScale));
    d3.select(yAxis).call(d3.axisLeft(yScale).tickFormat(d => String(d % 24).padStart(2, "0") + ":00"));
    }   

    let yAxisGridlines;
    $:{
    
	d3.select(yAxisGridlines).call(
		d3.axisLeft(yScale)
		  .tickFormat("")
          .tickSize(-usableArea.width)
	);

    }

    let hoveredIndex = -1;
    $: hoveredCommit = commits[hoveredIndex] ?? {};

    let cursor = {x: 0, y: 0};

      

    let svg;
    $: {
	d3.select(svg).call(d3.brush().on("start brush end", brushed));
    d3.select(svg).selectAll(".dots, .overlay ~ *").raise();

    }

    
   let brushSelection;
    $: brushSelection;
   
   function brushed (evt) {
        // console.log(evt);
	    brushSelection = evt.selection;
    } ;


    function isCommitSelected (commit) {
        console.log(brushSelection);
        if (!brushSelection) {
            return false;
        }
	
        let min = {x: brushSelection[0][0], y: brushSelection[0][1]};
        let max = {x: brushSelection[1][0], y: brushSelection[1][1]};
        let x = xScale(commit.date);
        let y = yScale(commit.hourFrac);
        console.log(x,y);
        return x >= min.x && x <= max.x && y >= min.y && y <= max.y;
    }
    
    $: selectedCommits = brushSelection ? commits.filter(isCommitSelected) : [];
    $: hasSelection = brushSelection && selectedCommits.length > 0;

    $: selectedLines = (hasSelection ? selectedCommits : commits).flatMap(d => d.lines);

    $: languageBreakdown= d3.rollups(selectedLines, v => v.length, d => d.type);
    


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


<svg viewBox="0 0 {width} {height}" bind:this={svg}>

    <g class="gridlines" transform="translate({usableArea.left}, 0)" bind:this={yAxisGridlines} />
    <g transform="translate(0, {usableArea.bottom})" bind:this={xAxis} />
    <g transform="translate({usableArea.left}, 0)" bind:this={yAxis} />


    <g class="dots">
        {#each commits as commit, index }
       
        <circle 
            on:mouseenter={evt => {hoveredIndex = index;
             cursor = {x: evt.x, y: evt.y};}}
            on:mouseleave={evt => (hoveredIndex = -1)}
            cx={xScale(commit.datetime)}
            cy={yScale(commit.hourFrac)}
            r="5"
            fill="steelblue"
            class:selected={isCommitSelected(commit)}
        />


        {/each}
    </g>

</svg>
<p>{hasSelection ? selectedCommits.length : "No"} commits selected</p>

<dl  class="tooltip" hidden={hoveredIndex === -1} style="top: {cursor.y}px; left: {cursor.x}px">
    
    <dt class="info">Commit</dt>
    <dd class="info"><a href="{ hoveredCommit.url }" target="_blank">{ hoveredCommit.id }</a></dd>

    <dt class="info">Date</dt>
    <dd class="info">{ hoveredCommit.datetime?.toLocaleString("en", {date: "full"}) }</dd>

    <dt class="info">Author</dt>
    <dd class="info">{ hoveredCommit.author }</dd>

    <dt class="info">Edited Lines</dt>
    <dd class="info">{ hoveredCommit.totalLines}</dd>

</dl>


<Pie data={Array.from(languageBreakdown).map(([language, lines]) => ({label: language, value: lines}))} />

<style>
    .selected{
        fill: red;
    }
</style>
