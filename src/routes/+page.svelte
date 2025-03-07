<script lang="ts">
    import { goto } from '$app/navigation';

    const menuItems = [
        { title: 'Cursor', description: '无限续杯', path: '/cursorTips' },
        { title: '图片调整', description: '调整图片尺寸', path: '/imageResize' },
        { title: 'Base64转换', description: '图片转Base64编码', path: '/imageBase64' },
        { title: '瀑布图', description: 'ECharts瀑布图示例', path: '/waterfall' }
    ];

    function navigateTo(path: string) {
        goto(path);
    }
</script>

<div class="menu-container">
    <h1>欢迎使用</h1>
    <div class="menu-grid">
        {#each menuItems as item}
            <button
                class="menu-card"
                on:click={() => navigateTo(item.path)}
                on:keydown={(e) => e.key === 'Enter' && navigateTo(item.path)}
            >
                <h2>{item.title}</h2>
                <p>{item.description}</p>
            </button>
        {/each}
    </div>
</div>

<style>
    .menu-container {
        text-align: center;
        padding: 1rem;
    }

    h1 {
        margin-bottom: 1.5rem;
        color: #333;
        font-size: 2rem;
        font-weight: 600;
    }

    .menu-grid {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(160px, 1fr));
        gap: 1rem;
        padding: 0.5rem;
        max-width: 1000px;
        margin: 0 auto;
    }

    .menu-card {
        background: white;
        padding: 1.2rem 1rem;
        border-radius: 8px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        cursor: pointer;
        transition: all 0.2s ease;
        border: none;
        width: 100%;
        display: flex;
        flex-direction: column;
        gap: 0.3rem;
    }

    .menu-card:hover {
        transform: translateY(-3px);
        box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
        background: #f8f9fa;
    }

    .menu-card h2 {
        margin: 0;
        color: #1a202c;
        font-size: 1.1rem;
        font-weight: 600;
        letter-spacing: 0.01em;
    }

    .menu-card p {
        margin: 0;
        color: #718096;
        font-size: 0.85rem;
        line-height: 1.4;
        font-weight: 400;
    }
</style> 