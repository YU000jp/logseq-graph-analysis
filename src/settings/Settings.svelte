<script lang="ts">
  import { graph, Mode, settings, store } from "../stores";
  import NodeFilter from "./components/NodeFilter.svelte";

  function handlShortestPathClear() {
    $settings.pathA = undefined;
    $settings.pathB = undefined;
  }
  function handleSearch(event) {
    if ($settings.filters[event.detail.id] != event.detail) {
      let filters = $settings.filters;
      filters[event.detail.id] = event.detail;
      $settings.filters = [...filters];
    }
  }
  function removeFilter(id) {
    $settings.filters = $settings.filters.filter((filter) => filter.id !== id);
  }

  function addFilter() {
    let id = 0;
    if ($settings.filters) {
      id = $settings.filters.length;
    }
    const newFilter = {
      id: id, // generate unique ID for new filter
      searchString: "",
      searchType: "color",
      searchColor: "#000000",
      foundNodeIds: [],
    };
    $settings.filters = [...$settings.filters, newFilter];
  }
</script>

<div class="settings">
  <div>
    <label for="search">search</label>
    <input type="text" bind:value={$settings.search} />
  </div>
  <div>
    <label for="filter">filter</label>
    <input type="checkbox" bind:checked={$settings.filter} />
  </div>
  {#if $settings.filter}
    <div>
      <label for="nhops">n hops</label>
      <input
        type="range"
        min="1"
        step="1"
        max="10"
        bind:value={$settings.filterLength}
      />
    </div>
  {/if}
  <div>
    <div class="filters">Filters <button on:click={addFilter}>+</button></div>
    {#each $settings.filters as { id, searchString, searchType, searchColor, foundNodeIds } (id)}
      <div class="filter">
        <NodeFilter
          {id}
          {searchString}
          {searchColor}
          {searchType}
          {foundNodeIds}
          on:search={handleSearch}
          on:destroyed={() => removeFilter(id)}
        />
        <button on:click={() => removeFilter(id)}>X</button>
      </div>
    {/each}
  </div>

  <div>
    <label for="labelThreshold">label threshold</label>
    <input
      type="range"
      min="0"
      step="0.05"
      max="10"
      bind:value={$settings.labelThreshold}
    />
  </div>
  <div>
    <label for="gravity">nodes gravity</label>
    <input
      type="range"
      min="0.01"
      step="0.01"
      max="20"
      bind:value={$settings.nodesGravity}
    />
  </div>
  <div>
    <label for="scalingRatio">scaling ratio</label>
    <input
      type="range"
      min="0.001"
      step="0.0001"
      max="1.5"
      bind:value={$settings.scalingRatio}
    />
  </div>
  <div>
    <label for="edgeWeightInfluence">edge weight influence</label>
    <input
      type="range"
      min="-2"
      step="0.1"
      max="2"
      bind:value={$settings.edgeWeightInfluence}
    />
  </div>
  {#if $settings.filter}
    <div>
      <label for="nhops">n hops</label>
      <input
        type="range"
        min="1"
        step="1"
        max="10"
        bind:value={$settings.filterLength}
      />
    </div>
  {/if}
  <div>
    <label for="size">size</label>
    <select id="size" name="size" bind:value={$settings.size}>
      <option selected value="in">inbound links</option>
      <option value="out">outbound links</option>
    </select>
  </div>
  <hr />
  <div>
    <select bind:value={$settings.mode}>
      {#each Object.entries(Mode) as [key, val]}
        <option value={val}>{val}</option>
      {/each}
    </select>
  </div>
  {#if $settings.mode === Mode.ShortestPath}
    <div>
      <input type="submit" value="clear" on:click={handlShortestPathClear} />
    </div>
    <div>
      <label for="directed">directed?</label>
      <input type="checkbox" bind:checked={$settings.directed} />
    </div>
    <div>
      <label for="path">path a</label>
      <input type="text" bind:value={$settings.pathA} />
    </div>
    <div>
      <label for="path">path b</label>
      <input type="text" bind:value={$settings.pathB} />
    </div>
  {/if}
  {#if $settings.mode === Mode.AdamicAdar || $settings.mode === Mode.CoCitation}
    <div>
      <label for="path">path a</label>
      <input type="text" bind:value={$settings.pathA} />
    </div>
    <div>
      <label for="bubbleSize">bubble size</label>
      <input
        type="range"
        min="0.01"
        step="0.1"
        max="10"
        bind:value={$settings.bubbleSize}
      />
    </div>
  {/if}
</div>

<style>
  .settings {
    position: fixed;
    top: 40px;
    right: 1vh;
    display: flex;
    flex-direction: column;
    border: 1px solid #a3a3a3;
    padding: 0.5rem;
    margin: 0;
    background-color: rgba(255, 255, 255, 0.8);
  }
  .filter {
    display: flex;
  }
</style>
