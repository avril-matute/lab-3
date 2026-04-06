<script>
    import * as d3 from 'd3';

    let width = 400;
    let height = 150;
    // let data = [
    //     { label: "A", value: 10 },
    //     { label: "B", value: 20 }
    // ];
    let margin = { top: 20, right: 10, bottom: 30, left: 60 };
    let innerWidth  = width  - margin.left - margin.right;
    let innerHeight = height - margin.top  - margin.bottom;
    let xAxis, yAxis;
    // let data = [
    // { label: "A", value: 10 },
    // { label: "B", value: 20 },
    // { label: "C", value: 15 },
    // { label: "D", value: 8 },
    // { label: "E", value: 25 }
    // ];
    export let data = [];
    export let title = "";
    export let hasSelection = false;
    $: maxBar = d3.greatest(data, d => d.value);
    $: yScale = d3.scaleBand()
    .domain(data.map(d => d.label))
    .range([0, innerHeight])
    .padding(0.1)


    $: xScale = d3.scaleLinear()
        .domain([0, d3.max(data, d => d.value) || 1])
        .range([0, innerWidth]);

    $: colorScale = d3.scaleOrdinal(d3.schemeTableau10)
        .domain(data.map(d => d.label));

    $: if (xAxis && yAxis) {
        d3.select(yAxis).call(d3.axisLeft(yScale));
        d3.select(xAxis).call(
            d3.axisBottom(xScale)
                .ticks(Math.min(d3.max(data, d => d.value), 10))
                // .tickValues(d3.range(0, d3.max(data, d => d.value) + 1))
    );

}


</script>

<div class="container" class:selected={hasSelection}>
    <svg viewBox="0 0 {width} {height}">
        <text
            x={margin.left + innerWidth / 2}
            y={margin.top / 2}
            text-anchor="middle"
            class="chart-title">
            {title}
        </text>
        <g transform="translate({margin.left}, {margin.top + innerHeight})">
            <g class="axis" bind:this={xAxis}/>
                <text
                    x={innerWidth / 2}
                    y={35}
                    text-anchor="middle"
                    class="axis-label">
                    Lines of Code
                </text>
            </g>

        <g transform="translate({margin.left}, {margin.top})">
            <g class="axis" bind:this={yAxis}/>
                <text
                    x={-(innerHeight / 2)}
                    y={-margin.left + 15}
                    text-anchor="middle"
                    transform="rotate(-90)"
                    class="axis-label">
                    Language
                </text>
            </g>
        <g transform="translate({margin.left}, {margin.top})">
            {#each data as d}
                <rect
                    x={0}
                    y={yScale(d.label)}
                    width={xScale(d.value)}
                    height={yScale.bandwidth()}
                    fill={colorScale(d.label)}
                />
            {/each}
            {#if maxBar}
                <!-- highlight outline around the tallest bar -->
                <rect
                    x={0}
                    y={yScale(maxBar.label)}
                    width={xScale(maxBar.value)}
                    height={yScale.bandwidth()}
                    fill="none"
                    stroke="currentColor"
                    stroke-width="2"
                />
                <!-- leader line -->
                <line
                    x1={xScale(maxBar.value)}
                    y1={yScale(maxBar.label) + yScale.bandwidth()/2}
                    x2={xScale(maxBar.value) + 15}
                    y2={yScale(maxBar.label) + yScale.bandwidth()/ 2}
                    stroke="currentColor"
                    stroke-width="1"
                />
                <!-- annotation text at end of leader line -->
                <text
                    x={xScale(maxBar.value)  + 15}
                    y={yScale(maxBar.label) +  yScale.bandwidth() / 2}
                    dominant-baseline="middle"
                    class="annotation">
                    Most lines of code
                </text>
            {/if}
        </g>
    </svg>
    <ul class="legend">
            {#each data as d}
                <li style="--color: {colorScale(d.label)}">
                    <span class="swatch"></span>
                    {d.label} <em>({d.value})</em>
                </li>
            {/each}
    </ul>
</div>

<style>
    svg {
        max-width: 80%;
        height: auto;
        overflow: visible;
    }

    .container {
        display:flex;
        padding: 20px;
        border: .3px solid oklab(61.641% -0.05593 -0.03142);
        flex-direction: column;
        border-radius: 7px;
        box-shadow: .5px .5px 5px oklab(71.399% 0.01478 -0.01804 / 0.584);
        transition: border-color 0.2s ease;

    }

    .container.selected {
        border-color: var(--color-select);
    }

    .legend {
        display:flex;
        list-style: none; /* Removes default bullets */
        padding: 1em;     /* Adds some breathing room */
        margin: 5px;
        flex-direction: row;
        justify-content: flex-end;
        gap: 1em;
        flex-wrap:wrap;
        font-size: .8em;
    }

    .swatch {
        display: inline-block; /* MUST have this to show width/height */
        background-color: var(--color);
        width: 15px;
        height: 15px;
        border-radius: 2px;
    }

    li {
        display: flex;
        align-items: center;
        gap: 8px; /* Give the color and text some space */
    }

    .chart-title {
        font-size: .5em;
        font-weight: bold;
        fill: currentColor;
    }

    .axis-label {
    font-size: 0.5em;
    fill: currentColor;
    color: gray;
    }

    .annotation {
        font-size: 0.5em;
        fill: currentColor;
        font-style: italic;
        /* background-color: white; */


    }

    .axis :global(.tick text) {
        font-size: 0.7em;
    }

</style>
