<script>
  import * as d3 from "d3";

  export let allPeople = [];
  export let people = [];
  export let metricX = "";
  export let metricY = "";
  let wrapper;

  let height = 500;
  $: width = height;

  $: xAccessor = d => {
    return d.powerstats[metricX];
  };

  $: yAccessor = d => {
    return d.powerstats[metricY];
  };

  $: xScale = d3
    .scaleLinear()
    .domain([0, 100])
    .range([0, width]);

  $: yScale = d3
    .scaleLinear()
    .domain([0, 100])
    .range([height, 0]);

  $: points = people.map(person => {
    return [xScale(xAccessor(person)), yScale(yAccessor(person))];
  });

  $: delaunay = d3.Delaunay.from(points);
  $: voronoi = delaunay.voronoi([0, 0, width, height]);

  let hoveredPerson;
</script>

<div class="wrapper" on:mousemove={(e)=>{
  const bounds = e.target.getBoundingClientRect()
  const x = e.clientX - bounds.left
  const y = e.clientY - bounds.top
  const pointIndex = delaunay.find(x,y)
  hoveredPerson = people[pointIndex]

}} bind:clientHeight={height} style="width: {width}px;">
{#each people as person}
  <div class="image" style="background-image: url({person.images.sm}); transform: translate(calc(-50% + {xScale(xAccessor(person))}px), calc(-50% + {yScale(yAccessor(person))}px));">
  </div>
{/each}
<div class="horizontal-line">higher {metricX} ➡</div>
<div class="vertical-line">higher {metricY} ⬆</div>
{#if hoveredPerson}
<div class="tooltip">{hoveredPerson.name}</div>
{/if}
</div>

<style>
  .wrapper {
    position: relative;
    display: flex;
    flex-direction: column;
    height: 80vh;
    margin: 1em auto;
  }
  .image {
    position: absolute;
    background-size: cover;
    border-radius: 100%;
    border: 3px solid transparent;
    overflow: hidden;
    transition: transform 0.3s ease-out;
    height: 20px;
    width: 20px;
  }
  .tooltip {
    position: absolute;
    top: 0;
    left: 0;
    font-size: 0.8em;
    transform: translateY(-50%);
    pointer-events: none;
  }
  .tooltip img {
    display: block;
    width: 4em;
  }
  .vertical-line {
    position: absolute;
    left: 50%;
    top: 0;
    bottom: 0;
    width: 0;
    border-right: 1px solid black;
    display: flex;
    align-items: flex-start;
    text-align: left;
    font-size: 0.7em;
    opacity: 0.6;
    font-style: italic;
    font-weight: 300;
  }
  .horizontal-line {
    position: absolute;
    top: 50%;
    left: 0;
    right: 0;
    border-bottom: 1px solid black;
    height: 0;
    display: flex;
    justify-content: flex-end;
    text-align: left;
    font-size: 0.7em;
    opacity: 0.6;
    font-style: italic;
    font-weight: 300;
  }
</style>


