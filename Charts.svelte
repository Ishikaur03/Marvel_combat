<script>
  // data from https://cdn.jsdelivr.net/gh/akabab/superhero-api@0.3.0/api/all.json
  import people from "./public/data.json";
  import { uniq } from "lodash";

  import Chart from "./Chart.svelte";

  const universes = [
    "Avengers",
    "Hulk",
    "Captain America",
    "Spider-man",
    "X-men",
    "Star Wars",
    "Batman",
    "Thor",
    "Deadpool",
    "Daredevil",
    "Fantastic Four",
    "Justice League",
    "Wonder Woman",
    "Superman",
    "Teenage Mutant Ninja Turtles"
  ];
  let universe = universes[0];
  const metrics = Object.keys(people[0].powerstats);
  let metricX = metrics[0];
  let metricY = metrics[1];

  $: universePeople = people.filter(
    d =>
      d.biography.firstAppearance
        .toLowerCase()
        .includes(universe.toLowerCase()) ||
      d.biography.publisher?.toLowerCase().includes(universe.toLowerCase()) ||
      d.connections.groupAffiliation
        ?.toLowerCase()
        .includes(universe.toLowerCase()) ||
      d.name.toLowerCase().includes(universe.toLowerCase())
  );
</script>

<select bind:value={universe} style="position: relative; padding: 0.3em; margin-bottom: 2em; z-index: 10">
  {#each universes as universe}
    <option value={universe}>{universe}</option>
  {/each}
</select>

<select bind:value={metricX} style="position: relative; padding: 0.3em; margin-bottom: 2em; z-index: 10">
  {#each metrics as metric}
    <option value={metric}>{metric}</option>
  {/each}
</select>

<select bind:value={metricY} style="position: relative; padding: 0.3em; margin-bottom: 2em; z-index: 10">
  {#each metrics as metric}
    <option value={metric}>{metric}</option>
  {/each}
</select>

<Chart people={universePeople} {metricX} {metricY} allPeople={people}  ></Chart>