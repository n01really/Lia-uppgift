<h1>Event</h1>
<!-- Future implementation: API integration for events. -->
 
<forms method="post">
    <label>
        Title:
        <input type="text" name="title" required />
    </label>
    <label>
        Date:
        <input type="date" name="date" required />
    </label>
    <button type="submit">Add Event</button>
</forms>
<forms method="post" action="?/delete">
    <button type="submit">Delete Event</button>
</forms>
<forms method="post" action="?/update">
    <button type="submit">Update Event</button>
</forms>
