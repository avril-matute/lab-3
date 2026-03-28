<script>
    import { base } from '$app/paths';
    import { onMount } from 'svelte';
    import * as d3 from 'd3';
    import BarHorizontal from '$lib/BarHorizontal.svelte';
    import {
        computePosition,
        autoPlacement,
        offset,
    } from '@floating-ui/dom';

    let locData = [];
    let wrangled = [];
    let commits = [];
    let width = 1000, height = 600;
    let margin = { top: 20, right: 20, bottom: 30, left: 60 };
    let usableArea = {
        top: margin.top,
        right: width - margin.right,
        bottom: height - margin.bottom,
        left: margin.left
    };
    let xAxis, yAxis, yAxisGridlines, xAxisGridlines;
    let hoveredIndex = -1;
    let cursor = {x: 0, y: 0};
    usableArea.width = usableArea.right - usableArea.left;
    usableArea.height = usableArea.bottom - usableArea.top;
    let commitTooltip;
    let tooltipPosition = {x: 0, y: 0};
    let clickedCommits = [];

    onMount(async () => {
        locData = await d3.csv(`${base}/loc.csv`, row => ({
            ...row,
            line: Number(row.line),
            depth: Number(row.depth),
            length: Number(row.length),
            date: new Date(row.date + "T00:00"+ row.timezone),
            datetime: new Date(row.datetime),
        }));
        wrangled = d3.rollups(
            locData,
            v => v.length,
            d => d.type,
        );
        commits = d3.groups(locData, d => d.commit).map(([commit, lines])=>{
            let first = lines[0];
            let {author, date, time, timezone, datetime} = first;
            let ret = {
                id: commit,
                url: "https://github.com/vis-society/lab-7/commit/" + commit,
                author, date, time, timezone, datetime,
                hourFrac: datetime.getHours() + datetime.getMinutes()/60,
                totalLines: lines.length,
                lines: lines
            };

            return ret;
        });
        commits = d3.sort(commits, d => -d.totalLines);
        console.log("Wrangled data:", wrangled);
        console.log(locData);
        console.log(commits);
    });

    $: chartData = wrangled.map(([language, lines]) => ({
            label: language,
            value: lines
        }));

    $: workByDay =  d3.rollups(
        locData,
        d=> d.length,
        d => d.datetime.toLocaleString('en', { weekday: 'long' }) // This extracts the day name (e.g., "Tuesday")
    );
    $: maxDay = d3.greatest(workByDay, d => d[1]); // d3.greatest looks at your rolled-up list (e.g., ["Tuesday", 42]), compares the numbers, and returns the entire pair that won. That way, we get both the number (42) and the label ("Tuesday").

    $: highestProductivityDay = maxDay ? maxDay[0] : 'Loading...';
    // scatter plot dates
    $: [minDate, maxDate] = d3.extent(commits.map(d => d.date));
    $: maxDatePlusOne = new Date(maxDate);
    $: maxDatePlusOne.setDate(maxDatePlusOne.getDate() + 1);

    $: xScale = d3.scaleTime()
                .domain([minDate, maxDatePlusOne])
                .range([usableArea.left, usableArea.right])
                .nice();

    $: yScale = d3.scaleLinear()
                .domain([24, 0])
                .range([usableArea.bottom, usableArea.top]);

    $: {
        d3.select(xAxis).call(d3.axisBottom(xScale));
        d3.select(yAxis).call(d3.axisLeft(yScale).tickFormat(d => String(d % 24).padStart(2, "0") + ":00"));
        d3.select(yAxisGridlines).call(
            d3.axisLeft(yScale)
            .tickFormat("")
            .tickSize(-usableArea.width)
        );
        d3.select(xAxisGridlines).call(
            d3.axisBottom(xScale)
            .tickFormat("")
            .tickSize(-usableArea.height)
        );
    }

    $: [minCommit, maxCommit] = d3.extent(commits.map(d => d.totalLines));
    $: rScale = d3.scaleSqrt()
        .domain([minCommit, maxCommit])
        .range([10, 50]);
    // $: maxCommitPlusOne = new Date(maxDate);
    // $: maxCommitPlusOne.setDate(maxCommitlusOne.getDate() + 1);

    $: hoveredCommit = commits[hoveredIndex] ?? hoveredCommit ?? {};

    // 1.
    $: selectedLines = (clickedCommits.length > 0 ? clickedCommits.flatMap(d => d.lines) : locData);

    //2.
    $: selectedCounts = d3.rollup(
        selectedLines,
        v => v.length,
        d => d.type
    );

    //3.
    $: allTypes = Array.from(new Set(locData.map(d => d.type)));

    //4.
    $: barData = allTypes.map(type =>  ({ label: String(type), value: selectedCounts.get(type) || 0 }));


    async function dotInteraction (index, evt) {
        let hoveredDot = evt.target;
        if (evt.type === "mouseenter") {
            hoveredIndex = index;
            cursor = {x: evt.x, y: evt.y};
            tooltipPosition = await computePosition(hoveredDot, commitTooltip, {
                strategy: "fixed", // because we use position: fixed
                middleware: [
                    offset(5), // spacing from tooltip to dot
                    autoPlacement() // see https://floating-ui.com/docs/autoplacement
                ],
            });
        } else if (evt.type === "mouseleave") {
            hoveredIndex = -1;
        } else if (evt.type === "click") {
           let commit = commits[index]
            if (!clickedCommits.includes(commit)) {
                // Add the commit to the clickedCommits array
                clickedCommits = [...clickedCommits, commit];
                console.log(clickedCommits);
            }
            else {
                    // Remove the commit from the array
                    clickedCommits = clickedCommits.filter(c => c !== commit);
            }
        }
    }
</script>

<h1> META</h1>

<dl class="stats">
	<dt>Total <abbr title="Lines of code">LOC</abbr></dt>
	<dd>{locData.length}</dd>
    <dt> Commits <abbr title="Total number of commits"></abbr></dt>
    <dd> {commits.length} </dd>
    <dt> Most Productive Day <abbr title="Day of week with the most lines of code committed "></abbr></dt>
    <dd> {highestProductivityDay} </dd>
</dl>

<h2> Commits by time of day</h2>
<svg viewBox="0 0 {width} {height}">
    <g class="gridlines" transform="translate({usableArea.left}, 0)" bind:this={yAxisGridlines} />
    <g class="gridlines" transform="translate(0, {usableArea.bottom})" bind:this={xAxisGridlines} />
    <g transform="translate(0, {usableArea.bottom})" bind:this={xAxis} />
    <g transform="translate({usableArea.left}, 0)" bind:this={yAxis} />
	<g class="dots">
    {#each commits as commit, index }
        <circle
            class:selected={ clickedCommits.includes(commit) }
            on:mouseenter={evt => dotInteraction(index, evt)}
	        on:mouseleave={evt => dotInteraction(index, evt)}
            on:click={ evt => dotInteraction(index, evt) }
            cx={ xScale(commit.datetime) }
            cy={ yScale(commit.hourFrac) }
            r={ rScale(commit.totalLines) }
        />
    {/each}
    </g>
</svg>

<dl class="info tooltip" hidden={hoveredIndex === -1} style="top: {tooltipPosition.y}px; left: {tooltipPosition.x}px" bind:this={commitTooltip}>
	<dt>Commit:</dt>
	<dd><a href="{ hoveredCommit.url }" target="_blank">{ hoveredCommit.id }</a></dd>

	<dt>Date:</dt>
	<dd>{ hoveredCommit.datetime?.toLocaleString("en", {dateStyle: "full"}) }</dd>

    <dt>Time:</dt>
	<dd>{ hoveredCommit.time}</dd>

	<dt>Author:</dt>
	<dd>{ hoveredCommit.author}</dd>

     <dt>Lines Editted:</dt>
	<dd>{ hoveredCommit.totalLines}</dd>
</dl>

<BarHorizontal
     data={barData}
     title={clickedCommits.length > 0 ? "Lines by language (Selected)" : "Lines by language (All)"}
     />

<style>
	svg {
		overflow: visible;
	}
    .gridlines {
        stroke-opacity: .1;
    }

    circle{
        /* Length and percentage values */
        /* r: rScale(commits.totalLines); */
        /* decided on 15-50 */
        /* r: 20%; */

        /* Global values */
        /* r: inherit;
        r: initial;
        r: revert;
        r: revert-layer;
        r: unset; */

        /* stroke: oklab(48.333% 0.02681 -0.10697); */
        /* stroke-opacity: .5; */
        opacity: .5;
        fill: oklab(61.641% -0.05593 -0.03142);
        transition: 200ms;
        &:hover {
            fill:oklab(55.251% 0.10952 -0.06663);
        }
    }
    /* dl.info with grid layout so that the <dt>s are on the 1st column
    and the <dd>s on the 2nd, remove their default margins, and apply
    some styling to make the labels less prominent than the values.
    .tooltip with position: fixed to it and top: 1em; and left: 1em;
    to place it at the top left of the viewport so we can see it regardless of scroll status. */
    dl.info{
        display: grid;
        margin: 0;
        grid-template-columns: auto 2fr;
        background-color: oklch(100% 0% 0 / 80%);
        box-shadow: .5px .5px 5px oklab(32.824% 0.01368 -0.01773 / 0.701);
        padding: 4px;
        border-radius: 7px;
        backdrop-filter: blur(10px);
        transition-duration: 500ms;
        transition-property: opacity, visibility;

        &[hidden]:not(:hover, :focus-within) {
            opacity: 0;
            visibility: hidden;
	}
    }



    dl.info dt{
        grid-column: 1;
        color: #666;
        font-size: 0.9em;
        font-weight: normal;
    }
    dl.info dd{
        grid-column: 2;
        font-size: 0.9em;
    }
    .tooltip{
        position: fixed;
        top: 1em;
        left: 1em;

    }

    .selected {
    fill: var(--color-accent);
    }

</style>
