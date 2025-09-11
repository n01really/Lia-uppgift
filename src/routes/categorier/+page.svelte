<script>
<<<<<<< HEAD
  import { onMount } from "svelte";
=======
  // Lista för att lagra kategorier
>>>>>>> main
  let categories = [];
  // Formulärfält för namn och beskrivning
  let name = "";
  let description = "";
  let loading = false;
  let error = "";

  // Hämtar alla kategorier från API:et
  async function loadCategories() {
    loading = true;
    error = "";
    try {
      const res = await fetch("http://localhost:5217/categories");
      if (!res.ok) throw new Error(await res.text());
      categories = await res.json();
    } catch (e) {
      error = e.message;
    } finally {
      loading = false;
    }
  }

  // Lägger till en ny kategori via formuläret
  async function addCategory(e) {
<<<<<<< HEAD
    e.preventDefault();
    if (!name || !description) return;
    loading = true;
    error = "";
    try {
      const res = await fetch("http://localhost:5217/categories", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ name, description })
      });
      if (!res.ok) throw new Error(await res.text());
      name = "";
      description = "";
      await loadCategories();
    } catch (e) {
      error = e.message;
    } finally {
      loading = false;
    }
=======
    e.preventDefault(); // förhindra att sidan laddas om
    if (!name || !description) return;

    const res = await fetch("http://localhost:5217/categories", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ name, description })
    });

    if (!res.ok) {
      // Felhantering om API-anropet misslyckas
      console.error("Failed to create category:", await res.text());
      return;
    }

    // Tömmer formuläret och laddar om kategorier
    name = "";
    description = "";
    await loadCategories();
>>>>>>> main
  }

  // Tar bort en kategori med angivet id
  async function deleteCategory(id) {
    loading = true;
    error = "";
    try {
      const res = await fetch(`http://localhost:5217/categories/${id}`, { method: "DELETE" });
      if (!res.ok) throw new Error(await res.text());
      await loadCategories();
    } catch (e) {
      error = e.message;
    } finally {
      loading = false;
    }
  }

<<<<<<< HEAD
  let editingId = null;
  let editName = "";
  let editDescription = "";
=======
  // Uppdaterar en kategori efter prompt från användaren
  async function updateCategory(id) {
    const newName = prompt("New name?");
    const newDescription = prompt("New description?");
    if (!newName || !newDescription) return;
>>>>>>> main

  function startEdit(cat) {
    editingId = cat.id;
    editName = cat.name;
    editDescription = cat.description;
  }

<<<<<<< HEAD
  function cancelEdit() {
    editingId = null;
    editName = "";
    editDescription = "";
  }

  async function saveEdit(id) {
    if (!editName || !editDescription) return;
    loading = true;
    error = "";
    try {
      const res = await fetch(`http://localhost:5217/categories/${id}`, {
        method: "PUT",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ name: editName, description: editDescription })
      });
      if (!res.ok) throw new Error(await res.text());
      editingId = null;
      editName = "";
      editDescription = "";
      await loadCategories();
    } catch (e) {
      error = e.message;
    } finally {
      loading = false;
    }
  }

  onMount(loadCategories);
=======
  // Laddar kategorier när sidan laddas
  loadCategories();
>>>>>>> main
</script>


<!-- Rubrik för sidan -->
<h1>Categories</h1>

<<<<<<< HEAD
{#if error}
  <p style="color: red;">{error}</p>
{/if}

=======

<!-- Formulär för att lägga till en ny kategori -->
>>>>>>> main
<form on:submit={addCategory}>
  <label>
    Name:
    <input type="text" bind:value={name} required disabled={loading} />
  </label>
  <label>
    Description:
    <textarea bind:value={description} required disabled={loading}></textarea>
  </label>
  <button type="submit" disabled={loading}>Add Category</button>
</form>

<<<<<<< HEAD
{#if loading}
  <p>Loading...</p>
{/if}

=======

<!-- Lista med alla kategorier och knappar för att ta bort eller redigera -->
>>>>>>> main
<ul>
  {#each categories as cat}
    <li>
      {#if editingId === cat.id}
        <input type="text" bind:value={editName} required disabled={loading} />
        <input type="text" bind:value={editDescription} required disabled={loading} />
        <button on:click={() => saveEdit(cat.id)} disabled={loading}>Save</button>
        <button on:click={cancelEdit} disabled={loading}>Cancel</button>
      {:else}
        {cat.name} - {cat.description}
        <button on:click={() => deleteCategory(cat.id)} disabled={loading}>Delete</button>
        <button on:click={() => startEdit(cat)} disabled={loading}>Edit</button>
      {/if}
    </li>
  {/each}
</ul>