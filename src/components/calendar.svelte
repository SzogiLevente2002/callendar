<script>
  import { onMount } from 'svelte';
  
  let currentDate = new Date();
  let month = currentDate.getMonth();
  let year = currentDate.getFullYear();
  let days = [];
  let dayNames = ['Hétfő', 'Kedd', 'Szerda', 'Csütörtök', 'Péntek', 'Szombat', 'Vasárnap'];
  let monthNames = ['Január', 'Február', 'Március', 'Április', 'Május', 'Június', 'Július', 'Augusztus', 'Szeptember', 'Október', 'November', 'December'];
  let events = {};
  
  // Formhoz kapcsolódó változók
  let selectedDay = '';
  let newEventText = '';

  function updateDays() {
    days = [];
    let firstDay = new Date(year, month, 1).getDay();
    firstDay = (firstDay === 0) ? 6 : firstDay - 1;
    let lastDate = new Date(year, month + 1, 0).getDate();
    
    for (let i = 0; i < firstDay; i++) {
      days.push(null);
    }
    
    for (let i = 1; i <= lastDate; i++) {
      days.push(i);
    }
  }

  function prevMonth() {
    month--;
    if (month < 0) {
      month = 11;
      year--;
    }
    updateDays();
  }

  function nextMonth() {
    month++;
    if (month > 11) {
      month = 0;
      year++;
    }
    updateDays();
  }

  function addEvent() {
    if (!selectedDay || !newEventText) return;
    let day = parseInt(selectedDay);
    if (!events[day]) events[day] = [];
    events[day].push(newEventText);
    newEventText = '';
  }

  function deleteEvent(day, index) {
    if (events[day]) {
      events[day].splice(index, 1);
      if (events[day].length === 0) {
        delete events[day];
      }
    }
  }

  function handleEventAdd(day) {
    selectedDay = day;
  }

  function handleEventDelete(day, index) {
    const confirmDelete = confirm("Biztosan törölni szeretnéd az eseményt?");
    if (confirmDelete) {
      deleteEvent(day, index);
    }
  }

  onMount(updateDays);
</script>

<style>
  body {
    font-family: Arial, sans-serif;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    height: 100vh;
    background-color: #f4f4f4;
    color: black;
  }
  .calendar-container {
    background: azure;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    text-align: center;
    color: black;
  }
  .calendar {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
    text-align: center;
    margin-top: 10px;
  }
  .day {
    padding: 10px;
    border: 1px solid #ccc;
    background: #fff;
    border-radius: 5px;
    color: black;
    position: relative;
    cursor: pointer;
    transition: background 0.3s ease, box-shadow 0.3s ease;
  }

  .day:hover {
    background: #e0f7fa;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  }
  .controls {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 10px;
    color: black;
  }
  .month-title {
    font-size: 24px;
    font-weight: bold;
    margin-bottom: 10px;
    color: black;
  }
  .day-names {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    font-weight: bold;
    margin-bottom: 5px;
    color: black;
  }
  button {
    padding: 5px 10px;
    border: none;
    background: #0da912;
    color: white;
    border-radius: 5px;
    cursor: pointer;
  }
  button:hover {
    background: #0da912;
  }
  .event {
    background-color: #ffeb3b;
    padding: 5px;
    margin-top: 5px;
    border-radius: 3px;
    font-size: 12px;
    color: black;
  }
  .event:hover {
    background-color: green;
  }
  .delete-btn {
    background-color: #f44336;
    padding: 2px 5px;
    margin-top: 5px;
    font-size: 10px;
    border-radius: 5px;
    color: white;
    cursor: pointer;
  }
  .delete-btn:hover {
    background-color: #d32f2f;
  }

  .event-form {
    margin-top: 10px;
    display: flex;
    flex-direction: column;
    font-size: 9px;
  }

  .event-form input {
    margin-bottom: 5px;
    padding: 5px;
  }
</style>

<div class="calendar-container">
  <div class="controls">
    <button on:click={prevMonth}>Előző</button>
    <span class="month-title">{monthNames[month]} {year}</span>
    <button on:click={nextMonth}>Következő</button>
  </div>

  <div class="day-names">
    {#each dayNames as dayName}
      <div>{dayName}</div>
    {/each}
  </div>

  <div class="calendar">
    {#each days as day}
      <div class="day" on:click={() => handleEventAdd(day)}>
        {day ? day : ''}
        {#if events[day]}
          {#each events[day] as event, index}
            <div class="event">
              {event}
              <button class="delete-btn" on:click={(e) => { e.stopPropagation(); handleEventDelete(day, index); }}>Törlés</button>
            </div>
          {/each}
        {/if}
      </div>
    {/each}
  </div>

  {#if selectedDay}
    <div class="event-form">
      <h3>Új esemény hozzáadása - {selectedDay}</h3>
      <input type="text" bind:value={newEventText} placeholder="Esemény neve" />
      <button on:click={addEvent}>Hozzáadás</button>
    </div>
  {/if}
</div>
