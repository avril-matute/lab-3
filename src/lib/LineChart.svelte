<script>
    import * as d3 from 'd3';
    export let data = [];
    let width = 875, height = 400;
    let margin = { top: 20, right: 10, bottom: 40, left: 50 };
    let xAxis, yAxis;
    let hoveredDay = null; // e.g. "Monday"

    $: usableArea = {
        top: margin.top,
        right: width - margin.right,
        bottom: height - margin.bottom,
        left: margin.left
    };

    $: xScale = d3.scaleTime()
        .domain(d3.extent(data, d => d.date))
        .range([usableArea.left, usableArea.right]);

    $: yScale = d3.scaleLinear()
        .domain([0, d3.max(data, d => d.count)])
        .range([usableArea.bottom, usableArea.top])
        .nice();

    $: line = d3.line()
        .x(d => xScale(d.date))
        .y(d => yScale(d.count))
        .curve(d3.curveBumpX);

    $: if (xAxis && yAxis) {
        d3.select(yAxis).call(d3.axisLeft(yScale));
        d3.select(xAxis).call(
            d3.axisBottom(xScale)
                // .ticks(Math.min(d3.max(data, d => d.value), 10))
                // .tickValues(d3.range(0, d3.max(data, d => d.value) + 1))
        );
    }

    $: dayRegions = (() => {
        // if there's no data, there are no regions!
        if (data.length === 0) return [];
        return data.map((d, i, arr) => {
            // get the previous date, if it exists
            const prev = arr[i - 1];
            // get the next date, if it exists
            const next = arr[i + 1];
            // define the left side of the region, which might be the edge of the axis if this is the first date
            const left = prev ? new Date((d.date.getTime() + prev.date.getTime()) / 2) : d.date;
            // define the right side of the region, which might be the edge of the axis if this is the last date
            const right = next ? new Date((d.date.getTime() + next.date.getTime()) / 2) : d.date;

            return {
                date: d.date,
                weekday: d.date.toLocaleString("en", { weekday: "long" }), // e.g. "Monday"
                x: xScale(left),
                width: xScale(right) - xScale(left),
            };
        });
    })();

    $: console.log(hoveredDay);

</script>


<div class="container">
    <h3 class="chart-title">
    Lines Edited {hoveredDay ? `on ${hoveredDay}` : 'by Day'}
    </h3>
    <svg viewBox="0 0 {width} {height}" on:mouseleave={() => hoveredDay = null}>

        {#each dayRegions as region}
            {#if region.weekday==hoveredDay}
                <rect
                    x={region.x}
                    y={usableArea.top}
                    width={region.width}
                    height={usableArea.bottom - usableArea.top}
                    fill="var(--color-select)"
                    opacity="0.2"
                    role="button"
                />
            {/if}
        {/each}

        <path
            d={line(data)}
            fill="none"
            stroke="steelblue"
            stroke-width="2"
        />
        <!-- dots at each data point -->
        {#each data as d}
            {@const isHighlighted = d.date.toLocaleString("en", { weekday: "long" }) === hoveredDay}
            <circle
                cx={xScale(d.date)}
                cy={yScale(d.count)}
                r="3"
                fill="steelblue"
            />
            {#if isHighlighted}
                <text
                    x={xScale(d.date)}
                    y={usableArea.top + 15}
                    text-anchor="middle"
                    font-size="12"
                    fill="var(--color-accent)"
                >
                    {Math.round(d.count)}
                </text>
            {/if}
        {/each}
        <g transform="translate(0, {usableArea.bottom})">
            <g class="axis" bind:this={xAxis}/>
        </g>

        <text
            x={usableArea.left + (usableArea.right - usableArea.left) / 2}
            y={height - 5}
            text-anchor="middle"
            class="axis-label">
            Date
        </text>

        <g transform="translate({usableArea.left}, 0)">
            <g class="axis" bind:this={yAxis}/>
        </g>

        <text
            x={-(usableArea.top + (usableArea.bottom - usableArea.top) / 2)}
            y={10}
            text-anchor="middle"
            transform="rotate(-90)"
            class="axis-label">
            Number of Lines Edited
        </text>


        <!-- invisible hover regions -->
        {#each dayRegions as region}
            <rect
                x={region.x}
                y={usableArea.top}
                width={region.width}
                height={usableArea.bottom - usableArea.top}
                fill="transparent"
                role="button"
                on:mouseenter={() => hoveredDay = region.weekday}
            />
        {/each}
    </svg>
</div>


<style>
	svg {
		overflow: visible;
        max-width: 80%;
        height: auto;
	}

    .container {
        display:flex;
        justify-content: center; /* Centers horizontally (Main Axis) */
        align-items: center;     /* Centers vertically (Cross Axis) */
        padding: 20px;
        border: .3px solid var(--color-accent);
        flex-direction: column;
        border-radius: 7px;
        box-shadow: .5px .5px 5px oklab(71.399% 0.01478 -0.01804 / 0.584);

    }

    .chart-title {
        font-size: 1.15em;
        font-weight: bold;
        fill: currentColor;
        text-align: center;
    }

    .axis-label {
        font-size: 1em;
        fill: currentColor;
        color: gray;
    }

    /* .axis :global(.tick text) {
        font-size: 0.7em;
    } */
</style>
