<script>
  let currentDate = new Date();
  let weekDays = [];
  let selectedDate = null;
  let events = {};
  let newEventText = '';

  // Hét generálása az aktuális dátum körül
  function generateWeek() {
    const start = new Date(currentDate);
    start.setDate(currentDate.getDate() - currentDate.getDay() + 1);

    weekDays = [];
    for (let i = 0; i < 7; i++) {
      const day = new Date(start);
      day.setDate(start.getDate() + i);
      weekDays.push(day);
    }
  }

  function prevWeek() {
    currentDate.setDate(currentDate.getDate() - 7);
    generateWeek();
  }

  function nextWeek() {
    currentDate.setDate(currentDate.getDate() + 7);
    generateWeek();
  }

  function selectCell(day, hour) {
    selectedDate = { day, hour };
    newEventText = '';
  }

  function addEvent() {
    if (!selectedDate || !newEventText) return;

    const key = selectedDate.day.toDateString();
    if (!events[key]) events[key] = {};
    if (!events[key][selectedDate.hour]) events[key][selectedDate.hour] = [];

    events[key][selectedDate.hour].push(newEventText);
    newEventText = '';
    selectedDate = null;
  }

  function deleteEvent(dayKey, hour, index) {
    const confirmDelete = confirm("Biztosan törölni szeretnéd az eseményt?");
    if (confirmDelete) {
      events[dayKey][hour].splice(index, 1);
      if (events[dayKey][hour].length === 0) delete events[dayKey][hour];
      if (Object.keys(events[dayKey]).length === 0) delete events[dayKey];
    }
  }

  generateWeek();
</script>

<style>
  .week-calendar {
    display: grid;
    grid-template-columns: 80px repeat(7, 1fr);
    border: 1px solid #ccc;
  }

  .cell, .time {
    border: 1px solid #eee;
    min-height: 60px;
    padding: 4px;
    font-size: 12px;
  }

  .header {
    background: #f0f0f0;
    font-weight: bold;
    text-align: center;
    padding: 6px;
  }

  .selected {
    background-color: #e3f2fd;
  }

  .event {
    background-color: #ffd54f;
    padding: 2px 4px;
    margin: 2px 0;
    border-radius: 3px;
    font-size: 11px;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .controls {
    margin: 10px 0;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  button {
    font-size: 12px;
    padding: 6px 10px;
    background: #0bc014;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }

  button:hover {
    background: #0bc014;
  }

  .event-form {
    margin-top: 10px;
    display: flex;
    gap: 10px;
    align-items: center;
  }

  input {
    font-size: 12px;
    padding: 4px;
  }

  .delete-btn {
    font-size: 10px;
    background: transparent;
    color: red;
    border: none;
    cursor: pointer;
  }

  h2 {
    font-size: 16px;
    text-align: center;
  }
</style>

<div class="controls">
  <button on:click={prevWeek}>◀ Előző hét</button>
  <h2>{weekDays[0]?.toLocaleDateString('hu-HU')} – {weekDays[6]?.toLocaleDateString('hu-HU')}</h2>
  <button on:click={nextWeek}>Következő hét ▶</button>
</div>

<div class="week-calendar">
  <div class="header"></div>
  {#each weekDays as day}
    <div class="header">
      {day.toLocaleDateString('hu-HU', { weekday: 'short', day: 'numeric', month: 'numeric' })}
    </div>
  {/each}

  {#each Array(24) as _, hour}
    <div class="time">{hour}:00</div>
    {#each weekDays as day}
      <div
        class="cell {selectedDate && selectedDate.day.getTime() === day.getTime() && selectedDate.hour === hour ? 'selected' : ''}"
        on:click={() => selectCell(day, hour)}
      >
        {#if events[day.toDateString()] && events[day.toDateString()][hour]}
          {#each events[day.toDateString()][hour] as evt, idx}
            <div class="event">
              {evt}
              <button class="delete-btn" on:click={() => deleteEvent(day.toDateString(), hour, idx)}>x</button>
            </div>
          {/each}
        {/if}
      </div>
    {/each}
  {/each}
</div>

{#if selectedDate}
  <div class="event-form">
    <input type="text" bind:value={newEventText} placeholder="Esemény neve" />
    <button on:click={addEvent}>Hozzáadás</button>
  </div>
{/if}
