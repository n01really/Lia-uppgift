<script>
  let categories = [];
  let name = "";
  let description = "";

  async function loadCategories() {
    const res = await fetch("http://localhost:5217/categories");
    categories = await res.json();
  }

  async function addCategory(e) {
    e.preventDefault(); // prevent default form submission
    if (!name || !description) return;

    const res = await fetch("http://localhost:5217/categories", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ name, description })
    });

    if (!res.ok) {
      console.error("Failed to create category:", await res.text());
      return;
    }

    name = "";
    description = "";
    await loadCategories();
  }

  async function deleteCategory(id) {
    await fetch(`http://localhost:5217/categories/${id}`, { method: "DELETE" });
    await loadCategories();
  }

  async function updateCategory(id) {
    const newName = prompt("New name?");
    const newDescription = prompt("New description?");
    if (!newName || !newDescription) return;

    await fetch(`http://localhost:5217/categories/${id}`, {
      method: "PUT",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ name: newName, description: newDescription })
    });

    await loadCategories();
  }

  loadCategories();
</script>

<h1>Categories</h1>

<form on:submit={addCategory}>
  <label>
    Name:
    <input type="text" bind:value={name} required />
  </label>
  <label>
    Description:
    <textarea bind:value={description} required></textarea>
  </label>
  <button type="submit">Add Category</button>
</form>

<ul>
  {#each categories as cat}
    <li>
      {cat.name} - {cat.description}
      <button on:click={() => deleteCategory(cat.id)}>Delete</button>
      <button on:click={() => updateCategory(cat.id)}>Edit</button>
    </li>
  {/each}
</ul>
