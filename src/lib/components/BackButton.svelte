<script lang="ts">
    import { goto } from '$app/navigation';
</script>

<button class="back-button" on:click={() => goto('/')}>
    <slot>返回主页</slot>
</button>

<style>
    .back-button {
        position: fixed;
        bottom: 2rem;
        right: 2rem;
        padding: 0.75rem 1.5rem;
        background: #4a5568;
        color: white;
        border: none;
        border-radius: 8px;
        cursor: pointer;
        transition: all 0.2s;
        box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
        z-index: 100;
    }

    .back-button:hover {
        background: #2d3748;
        transform: translateY(-2px);
        box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
    }
</style> 