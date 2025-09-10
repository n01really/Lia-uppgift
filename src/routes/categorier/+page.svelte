<script>
  // Lista för att lagra kategorier
  let categories = [];
  // Formulärfält för namn och beskrivning
  let name = "";
  let description = "";

  // Hämtar alla kategorier från API:et
  async function loadCategories() {
    const res = await fetch("http://localhost:5217/categories");
    categories = await res.json();
  }

  // Lägger till en ny kategori via formuläret
  async function addCategory(e) {
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
  }

  // Tar bort en kategori med angivet id
  async function deleteCategory(id) {
    await fetch(`http://localhost:5217/categories/${id}`, { method: "DELETE" });
    await loadCategories();
  }

  // Uppdaterar en kategori efter prompt från användaren
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

  // Laddar kategorier när sidan laddas
  loadCategories();
</script>


<!-- Rubrik för sidan -->
<h1>Categories</h1>


<!-- Formulär för att lägga till en ny kategori -->
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


<!-- Lista med alla kategorier och knappar för att ta bort eller redigera -->
<ul>
  {#each categories as cat}
    <li>
      {cat.name} - {cat.description}
      <button on:click={() => deleteCategory(cat.id)}>Delete</button>
      <button on:click={() => updateCategory(cat.id)}>Edit</button>
    </li>
  {/each}
</ul>
