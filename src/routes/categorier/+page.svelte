<h1>Categories</h1>
<!-- Future implementation: API integration for categories. -->

<forms method="post">
    <label>
        Name:
        <input type="text" name="name" required />
    </label>
    <label>
        Description:
        <input type="text" name="description" required />
    </label>
    <button type="submit">Add Category</button>
</forms>
<form method="post" action="?/delete">
    <button type="submit">Delete Category</button>
</form>
<form method="post" action="?/update">
    <button type="submit">Update Category</button>
</form>
<!--<ul>
    {#each categories as category}
        <li>{category.name} - {category.description}</li>
    {/each}
</ul>-->