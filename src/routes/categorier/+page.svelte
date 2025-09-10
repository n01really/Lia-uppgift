<script>
  import { onMount } from "svelte";
  let categories = [];
  let name = "";
  let description = "";
  let loading = false;
  let error = "";

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

  async function addCategory(e) {
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
  }

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

  let editingId = null;
  let editName = "";
  let editDescription = "";

  function startEdit(cat) {
    editingId = cat.id;
    editName = cat.name;
    editDescription = cat.description;
  }

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
</script>

<h1>Categories</h1>

{#if error}
  <p style="color: red;">{error}</p>
{/if}

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

{#if loading}
  <p>Loading...</p>
{/if}

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