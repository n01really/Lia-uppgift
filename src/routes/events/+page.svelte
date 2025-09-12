<script>
  import { onMount } from "svelte";

  let events = [];
  let categories = [];
  let selectedCategoryId = null;

  let title = "";
  let location = "";
  let description = "";

  async function loadCategories() {
    const res = await fetch("http://localhost:5217/categories");
    categories = await res.json();

    if (categories.length > 0) {
      selectedCategoryId = categories[0].id;
      await loadEvents();
    }
  }

  async function loadEvents() {
    const res = await fetch(`http://localhost:5217/categories/${selectedCategoryId}/events`);
    events = await res.json();
  }

  async function addEvent(e) {
    e.preventDefault();

    const now = new Date().toISOString();

    await fetch("http://localhost:5217/events", {
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

    title = "";
    location = "";
    description = "";

    await loadEvents();
  }

  async function deleteEvent(id) {
    await fetch(`http://localhost:5217/events/${id}`, {
      method: "DELETE"
    });
    await loadEvents();
  }

  async function editEvent(ev) {
    const newTitle = prompt("New title:", ev.eventName);
    const newLocation = prompt("New location:", ev.location);
    const newDescription = prompt("New description:", ev.description);

    await fetch(`http://localhost:5217/events/${ev.id}`, {
      method: "PUT",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        eventName: newTitle,
        eventDate: ev.eventDate,
        location: newLocation,
        description: newDescription
      })
    });

    await loadEvents();
  }

  onMount(loadCategories);
</script>

<h1>Events</h1>

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

<h2>Event List</h2>
<ul>
  {#each events as ev}
    <li>
      {ev.eventName} - {ev.location} - {ev.eventDate} - {ev.description}
      <button on:click={() => editEvent(ev)}>Edit</button>
      <button on:click={() => deleteEvent(ev.id)}>Delete</button>
      <p></p>
    </li>
  {/each}
</ul>
