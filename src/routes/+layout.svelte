<script>
    import '../app.css';
</script>

<div class="app">
    <main>
        <slot />
    </main>
</div>

<style>
    .app {
        min-height: 100vh;
        background-color: #f5f5f5;
    }

    main {
        max-width: 1200px;
        margin: 0 auto;
        padding: 2rem;
    }
</style>