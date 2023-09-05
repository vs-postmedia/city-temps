<script>
    import { csv } from "d3-fetch";
    import { onMount } from 'svelte';
    import { get } from 'svelte/store';
    // import { menuValue } from './stores.js';
    import Combobox from './components/combobox/Combobox.svelte';
	import LineChart from './components/chart/LineChart.svelte';

    
    // VARIABLES
    let data, promise, threshold, value;
    let van = [];
    const cities = ['Abbotsford', 'Vancouver'];
    let abby = [
        {x: new Date('2016-12-25'), y: 20},
        {x: new Date('2016-12-26'), y: 10},
        {x: new Date('2016-12-27'), y: 15}, 
        {x: new Date('2016-12-28'), y: 10}
    ];

    const testFile = 'https://vs-postmedia-data.sfo2.digitaloceanspaces.com/misc/test-data-02.csv';

    // REACTIVE VARS
    $: value, updateData(value);

    // FUNCTIONS
    function init() {
        console.log('INIT!')
        
        // get data
        promise = csv(testFile)
            .then(resolvedData => {
                van = resolvedData.map(d => {
                    return {
                        x: new Date(d.local_date),
                        y: +d.anomaly
                    }
                });
                // set VanCity as default
                updateData('vancouver');
            });
    }

    function updateData(value) {
        if (value === 'abbotsford') {
            data = abby;
        } else {
            data = van;
        }
    }

    onMount(init);
</script>


<!-- HTML -->
<header>
    <h1>VS SvelteKit Template</h1>
    <p class="subhead">Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>
</header>

<!-- Combobox to pick city -->
<Combobox bind:value
    label=''
    name='city'
    placeholder='Pick a city...'
    options={[
    	{ text: "Abbotsford", value: "abbotsford" },
    	{ text: "Vancouver", value: "vancouver" }
  	]}
/>
<pre class="status">Selected: {value || ''}</pre>

{#await promise}
    <h2>Loading...</h2>
{:then resolvedData}
    <LineChart data={data} />
{/await}



<style>
    @import '../css/normalize.css';
    @import '../css/fonts.css';
    @import '../css/colors.css';
    @import './page-styles.css';
</style>