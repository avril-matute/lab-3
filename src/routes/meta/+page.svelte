<script>
    import { base } from '$app/paths';
    import { onMount } from 'svelte';
    import * as d3 from 'd3';
    import BarHorizontal from '$lib/BarHorizontal.svelte';

    let locData = [];
    let wrangled = [];

    onMount(async () => {
        locData = await d3.csv(`${base}/loc.csv`, row => ({
            ...row,
            line: Number(row.line),
            length: Number(row.length),
            depth: Number(row.depth)
        }));
        wrangled = d3.rollups(
            locData,
            v => v.length,
            d => d.type,
        );
        console.log("Wrangled data:", wrangled);
    });

    $: chartData = wrangled.map(([language, lines]) => ({
            label: language,
            value: lines
        }));
</script>

<h1> META</h1>
<BarHorizontal data={chartData}/>
