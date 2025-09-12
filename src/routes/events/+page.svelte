<script>
  // Importerar onMount för att köra kod när komponenten laddas
  import { onMount } from "svelte";

  // Lista för att lagra event-objekt
  let events = [];
  let categories = [];
  let selectedCategoryId = null;

<<<<<<< HEAD
  // Formulärfält för att skapa nytt event
=======
>>>>>>> origin/Roretesting
  let title = "";
  let location = "";
  let description = "";

<<<<<<< HEAD
  // Lista med kategorier och valt kategori-id för dropdown
  let categories = [];
  let selectedCategoryId = null;

  // Hämtar alla kategorier från API:et och sätter första som vald
=======
>>>>>>> origin/Roretesting
  async function loadCategories() {
    const res = await fetch("http://localhost:5217/categories");
    categories = await res.json();

    if (categories.length > 0) {
      selectedCategoryId = categories[0].id;
      await loadEvents();
    }
  }

  // Hämtar alla events för vald kategori
  async function loadEvents() {
    const res = await fetch(`http://localhost:5217/categories/${selectedCategoryId}/events`);
    events = await res.json();
  }

  // Lägger till ett nytt event via formuläret
  async function addEvent(e) {
    e.preventDefault();

    const now = new Date().toISOString();

<<<<<<< HEAD
    const now = new Date().toISOString(); // nuvarande datum och tid

    const res = await fetch("http://localhost:5217/events", {
=======
    await fetch("http://localhost:5217/events", {
>>>>>>> origin/Roretesting
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        name: title,
        date: now,
        location,
        description,
        categoryId: selectedCategoryId
      })
    });

<<<<<<< HEAD
    if (!res.ok) {
      // Felhantering om API-anropet misslyckas
      console.error("Failed to add event:", await res.text());
      return;
    }

    // Tömmer formuläret och laddar om events
=======
>>>>>>> origin/Roretesting
    title = "";
    location = "";
    description = "";

    await loadEvents();
  }

  // Tar bort ett event med angivet id
  async function deleteEvent(id) {
    await fetch(`http://localhost:5217/events/${id}`, {
      method: "DELETE"
    });
    await loadEvents();
  }

  // Redigerar ett event efter prompt från användaren
  async function editEvent(ev) {
    const newTitle = prompt("New title:", ev.eventName);
    const newLocation = prompt("New location:", ev.location);
    const newDescription = prompt("New description:", ev.description);

    await fetch(`http://localhost:5217/events/${ev.id}`, {
      method: "PUT",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
<<<<<<< HEAD
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
=======
        eventName: newTitle,
        eventDate: ev.eventDate,
        location: newLocation,
        description: newDescription
      })
    });

    await loadEvents();
  }

  onMount(loadCategories);
>>>>>>> origin/Roretesting
</script>


<!-- Rubrik för sidan -->
<h1>Events</h1>


<!-- Formulär för att lägga till ett nytt event -->
<form on:submit={addEvent}>
  <input placeholder="Title" bind:value={title} />
  <input placeholder="Location" bind:value={location} />
  <select bind:value={selectedCategoryId} on:change={loadEvents}>
    {#each categories as cat}
      <option value={cat.id}>{cat.name}</option>
    {/each}
  </select>
  <input placeholder="Description" bind:value={description} />
  <button>Add Event</button>
</form>


<!-- Lista med alla events och knappar för att redigera eller ta bort -->
<h2>Event List</h2>
<ul>
  {#each events as ev}
    <li>
      {ev.eventName} - {ev.location}
      <button on:click={() => editEvent(ev)}>Edit</button>
      <button on:click={() => deleteEvent(ev.id)}>Delete</button>
      <p></p>
    </li>
  {/each}
</ul>
