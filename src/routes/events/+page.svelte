<script>
  import { onMount } from "svelte";

  let events = [];

  // Form fields
  let title = "";
  let location = "";
  let description = "";

  // Categories for dropdown
  let categories = [];
  let selectedCategoryId = null;

  async function loadCategories() {
    const res = await fetch("http://localhost:5217/categories");
    categories = await res.json();
    if (categories.length > 0) selectedCategoryId = categories[0].id;
  }

  async function loadEvents() {
    if (!selectedCategoryId) return;
    const res = await fetch(`http://localhost:5217/categories/${selectedCategoryId}/events`);
    events = await res.json();
  }

  async function addEvent(e) {
    e.preventDefault();

    if (!title || !location || !selectedCategoryId) return;

    const now = new Date().toISOString(); // current date & time

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
      console.error("Failed to add event:", await res.text());
      return;
    }

    title = "";
    location = "";
    description = "";

    await loadEvents();
  }

  async function deleteEvent(id) {
    await fetch(`http://localhost:5217/events/${id}`, { method: "DELETE" });
    await loadEvents();
  }

  async function editEvent(ev) {
    const newTitle = prompt("Title:", ev.eventName) || ev.eventName;
    const newLocation = prompt("Location:", ev.location) || ev.location;
    const newDescription = prompt("Description:", ev.description) || ev.description;

    const res = await fetch(`http://localhost:5217/events/${ev.id}`, {
      method: "PUT",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        EventName: newTitle,
        EventDate: ev.eventDate, // keep original date/time
        Location: newLocation,
        Description: newDescription
      })
    });

    if (!res.ok) {
      console.error("Failed to update event:", await res.text());
      return;
    }

    await loadEvents();
  }

  onMount(async () => {
    await loadCategories();
    await loadEvents();
  });

  $: if (selectedCategoryId) loadEvents();
</script>

<h1>Events</h1>

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
