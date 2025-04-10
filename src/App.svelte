<script>
  import Calendar from './components/calendar.svelte';
  import Weekly from './components/weekly.svelte';

  let isWeekView = false;  // Állapot, hogy melyik nézet van éppen aktiválva
</script>

<style>
  body {
    margin: 0;
    font-family: Arial, sans-serif;
    background-color: red;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }
  .app-container {
    width: 100%;
    max-width: 600px;
    background: rgb(216, 227, 5);
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  }
  h1 {
    text-align: center;
  }
  .view-toggle {
    text-align: center;
    margin-top: 20px;
  }
  button {
    padding: 10px 20px;
    background-color: #0bc014;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }
  button:hover {
    background-color: #0bc014;
  }
</style>

<div class="app-container">
  <h1>Naptár</h1>
  {#if isWeekView}
    <Weekly />
  {:else}
    <Calendar />
  {/if}

  <div class="view-toggle">
    <button on:click={() => isWeekView = !isWeekView}>
      {isWeekView ? 'Havi Nézet' : 'Heti Nézet'}
    </button>
  </div>
</div>
