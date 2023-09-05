<script>
    import * as d3 from 'd3';
    import { draw, fade, slide } from 'svelte/transition';

    export let data = [];
    export let width = 640;
    export let height = 400;
    export let marginTop = 20;
    export let marginRight = 20;
    export let marginBottom = 30;
    export let marginLeft = 40;

    // VARS
    let gx, gy;
    const yMax = d3.max(data, d => d.y);
    const xExtent = d3.extent(data, d => d.x);
   
    
    // reactive statements
    $: x = d3.scaleUtc().domain(xExtent).range([marginLeft, width - marginRight]);
    $: y = d3.scaleLinear([yMax * -1, yMax], [height - marginBottom, marginTop]).nice();
    $: zeroAxisLine = [{x: xExtent[0], y: 0}, {x: xExtent[1], y: 0}];

     
    // path generator
    $: line = d3.area()
        .curve(d3.curveStep)
        .defined(d => !isNaN(d.y))
        .x(d => x(d.x))
        .y1(d => y(d.y))
        .y0(y(0))

    // axis
    $: d3.select(gy).call(d3.axisLeft(y));
    $: d3.select(gx).call(d3.axisBottom(x).ticks(width / 80).tickSizeOuter(0));
</script>

<!-- <pre>{`LINE CHART: ${JSON.stringify(data)}`}</pre> -->
<svg width={width} height={height}>
    <!-- GRADIENT FOR COLOURING BASED ON THRESHOLD -->
    <defs>
        <linearGradient id="gradient-fill" gradientUnits="userSpaceOnUse" x1="0%" y1="0%" x2="0%" y2="100%">
            <stop offset="0%" stop-color="rgba(227, 93, 66, 0.4)"></stop>
            <stop offset="49%" stop-color="rgba(227, 93, 66, 0.4)"></stop>
            <stop offset="49%" stop-color="rgba(88, 160, 208, 0.4)"></stop>
            <stop offset="100%" stop-color="rgba(88, 160, 208, 0.4)"></stop>
        </linearGradient>

        <linearGradient id="gradient-path" gradientUnits="userSpaceOnUse" x1="0%" y1="0%" x2="0%" y2="100%">
            <stop offset="0%" stop-color="#E35D42"></stop>
            <stop offset="49%" stop-color="#E35D42"></stop>
            <stop offset="49%" stop-color="#58a0d0"></stop>
            <stop offset="100%" stop-color="#58a0d0"></stop>
        </linearGradient>
    </defs>

    <!-- draw axes -->
    <g bind:this={gx} transform="translate(0,{height - marginBottom})" />
    <g bind:this={gy} transform="translate({marginLeft},0)" />

    <!-- area path -->
    <path id="area-path"
        fill="url(#gradient-fill)"
        stroke="url(#gradient-path)"
        stroke-linecap="round"
        stroke-linejoin="round"
        stroke-width="1.5" 
        in:draw={{ duration: 2000 }} 
        d={line(data)}
    />

    <!-- Zero-axis line -->
    {#if zeroAxisLine[0].x !== undefined}
        <path fill="none" stroke="#231F20" stroke-width="2" d={line(zeroAxisLine)} />
    {/if}

    <!-- <g fill="white" stroke="currentColor" stroke-width="1.5">
        {#each data as d, i}
          <circle key={i} cx={x(i)} cy={y(d)} r="2.5" />
        {/each}
    </g> -->
</svg>
