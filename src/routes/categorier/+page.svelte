<h1>Categories</h1>
<!-- Future implementation: API integration for categories. -->

<forms method="post">
    <label>
        Name:
        <input type="text" name="name" required />
    </label>
    <button type="submit">Add Category</button>
</forms>
<forms method="post" action="?/delete">
    <button type="submit">Delete Category</button>
</forms>
<forms method="post" action="?/update">
    <button type="submit">Update Category</button>
</forms>