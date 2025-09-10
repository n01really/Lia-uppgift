<script>
  // Importerar onMount för att köra kod när komponenten laddas
  import { onMount } from "svelte";

  // Lista för att lagra event-objekt
  let events = [];

  // Formulärfält för att skapa nytt event
  let title = "";
  let location = "";
  let description = "";

  // Lista med kategorier och valt kategori-id för dropdown
  let categories = [];
  let selectedCategoryId = null;

  // Hämtar alla kategorier från API:et och sätter första som vald
  async function loadCategories() {
    const res = await fetch("http://localhost:5217/categories");
    categories = await res.json();
    if (categories.length > 0) selectedCategoryId = categories[0].id;
  }

  // Hämtar alla events för vald kategori
  async function loadEvents() {
    if (!selectedCategoryId) return;
    const res = await fetch(`http://localhost:5217/categories/${selectedCategoryId}/events`);
    events = await res.json();
  }

  // Lägger till ett nytt event via formuläret
  async function addEvent(e) {
    e.preventDefault();

    if (!title || !location || !selectedCategoryId) return;

    const now = new Date().toISOString(); // nuvarande datum och tid

    const res = await fetch("http://localhost:5217/events", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        name: title,
        date: now,
        location,
        description: description || title,
        categoryId: selectedCategoryId
      })
    });

    if (!res.ok) {
      // Felhantering om API-anropet misslyckas
      console.error("Failed to add event:", await res.text());
      return;
    }

    // Tömmer formuläret och laddar om events
    title = "";
    location = "";
    description = "";

    await loadEvents();
  }

  // Tar bort ett event med angivet id
  async function deleteEvent(id) {
    await fetch(`http://localhost:5217/events/${id}`, { method: "DELETE" });
    await loadEvents();
  }

  // Redigerar ett event efter prompt från användaren
  async function editEvent(ev) {
    const newTitle = prompt("Title:", ev.eventName) || ev.eventName;
    const newLocation = prompt("Location:", ev.location) || ev.location;
    const newDescription = prompt("Description:", ev.description) || ev.description;

    const res = await fetch(`http://localhost:5217/events/${ev.id}`, {
      method: "PUT",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        EventName: newTitle,
        EventDate: ev.eventDate, // behåll ursprungligt datum/tid
        Location: newLocation,
        Description: newDescription
      })
    });

    if (!res.ok) {
      // Felhantering om API-anropet misslyckas
      console.error("Failed to update event:", await res.text());
      return;
    }

    await loadEvents();
  }

  // Körs när komponenten laddas, hämtar kategorier och events
  onMount(async () => {
    await loadCategories();
    await loadEvents();
  });

  // Reagerar på ändring av kategori och laddar events
  $: if (selectedCategoryId) loadEvents();
</script>


<!-- Rubrik för sidan -->
<h1>Events</h1>


<!-- Formulär för att lägga till ett nytt event -->
<form on:submit={addEvent}>
  <label>
    Title:
    <input type="text" bind:value={title} required />
  </label>
  <label>
    Location:
    <input type="text" bind:value={location} required />
  </label>
  <label>
    Category:
    <select bind:value={selectedCategoryId}>
      {#each categories as cat}
        <option value={cat.id}>{cat.name}</option>
      {/each}
    </select>
  </label>
  <label>
    Description:
    <input type="text" bind:value={description} placeholder="Optional" />
  </label>
  <button type="submit">Add Event</button>
</form>


<!-- Lista med alla events och knappar för att redigera eller ta bort -->
<h2>Event List</h2>
<ul>
  {#each events as ev}
    <li>
      <b>{ev.eventName}</b> ({new Date(ev.eventDate).toLocaleString()}) - {ev.location} - {ev.description}
      <button on:click={() => editEvent(ev)}>Edit</button>
      <button on:click={() => deleteEvent(ev.id)}>Delete</button>
    </li>
  {/each}
</ul>
