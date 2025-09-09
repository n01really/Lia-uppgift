<script>
    let { children } = $props();
</script>

<nav>
    <a href="/">Home</a>
    <a href="/events">Events</a>
    <a href="/categorier">Categories</a>
</nav>

{@render children()}