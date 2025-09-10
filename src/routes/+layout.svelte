
<script>
    // Tar emot children-prop från SvelteKit (innehåll från undersidor)
    let { children } = $props();
</script>


<!-- Navigationsmeny för hela webbplatsen -->
<nav>
    <a href="/">Home</a>
    <a href="/events">Events</a>
    <a href="/categorier">Categories</a>
</nav>


<!-- Renderar innehållet från undersidor -->
{@render children()}