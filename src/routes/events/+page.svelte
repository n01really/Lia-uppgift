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
    <label>
        time:
        <input type="time" name="time" required />
    </label>
    <label>
        Location:
        <input type="text" name="location" required />
    </label>
    <label>
        Category:
        <input type="text" name="category" required />
    </label>
    <button type="submit">Add Event</button>
</forms>
<forms method="post" action="?/delete">
    <button type="submit">Delete Event</button>
</forms>
<forms method="post" action="?/update">
    <button type="submit">Update Event</button>
</forms>
<ul>
    {#each events as event}
        <li>{event.title} - {event.date} - {event.time} - {event.location} - {event.category}</li>
    {/each}
</ul>