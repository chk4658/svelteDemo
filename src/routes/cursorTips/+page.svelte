<script lang="ts">
    import {goto} from '$app/navigation';
    import BackButton from '$lib/components/BackButton.svelte';

    let copyMessage: string = '';
    let messageTimeout: ReturnType<typeof setTimeout>;

    function showCopyMessage(text: string) {
        copyMessage = text;
        if (messageTimeout) clearTimeout(messageTimeout);
        messageTimeout = setTimeout(() => {
            copyMessage = '';
        }, 2000);
    }

    async function copyToClipboard(text: string) {
        try {
            await navigator.clipboard.writeText(text);
            showCopyMessage('路径已复制到剪贴板');
        } catch (err) {
            showCopyMessage('复制失败，请重试');
        }
    }
</script>

<div class="tips-container">
    <h1>Cursor 使用技巧</h1>
    <div class="content">
        <ul>
            <li>关闭退出 <code>Cursor</code></li>
        </ul>

        <ul>
            <li>打开 <code>Cursor</code> 的 <code>storage.json</code> 文件</li>
        </ul>

        <ul class="file-paths">
            <li><code>Windows</code> 位置: <code>%APPDATA%\Cursor\User\globalStorage\storage.json</code>
                <div class="path-link" on:click={() => copyToClipboard('C:\\Users\\KHC1SZH\\AppData\\Roaming\\Cursor\\User\\globalStorage')}>
                    <code class="clickable">C:\Users\KHC1SZH\AppData\Roaming\Cursor\User\globalStorage</code>
                    <span class="click-hint">点击复制路径</span>
                </div>
            </li>

            <li><code>MacOS</code> 位置: <code>~/Library/Application\
                Support/Cursor/User/globalStorage/storage.json</code></li>

            <li><code>Linux</code> 位置: <code>~/.config/Cursor/User/globalStorage/storage.json</code></li>

            <li>关闭文件 <code class="highlight">只读</code> 权限：
                <ul>
                    <li><code>Windows</code>: 选择 <code>storage.json</code> 右键 -> 属性 -> 取消选中 <code
                            class="highlight">只读</code></li>

                    <li><code>MacOS</code> / <code>Linux</code>: 在目录内终端执行 <code>chmod 666 storage.json</code>
                    </li>
                </ul>
            </li>
        </ul>

        <ul>
            <li>找到以下 <code>Key</code> - <code>Value</code> 键值，修改 <code>64位指纹ID</code> 和
                <code>UUID代码</code> 对应的值，即随便替换修改几个数字即可。
            </li>
        </ul>

        <div class="code-block">
            <pre><code>{`{
    "telemetry.macMachineId": "64位指纹ID",
    "telemetry.machineId": "64位指纹ID",
    "telemetry.devDeviceId": "UUID代码"
}`}</code></pre>
        </div>

        <ul>
            <li>重新将文件恢复为 <code class="highlight">只读</code>
                <ul>
                    <li><code>Windows</code>: 选择 <code>storage.json</code> 右键 -> 属性 -> 选中 <code
                            class="highlight">只读</code></li>

                    <li><code>MacOS</code> / <code>Linux</code>: <code>chmod 444 storage.json</code></li>
                </ul>
            </li>
        </ul>

        <ul>
            <li>重启 <code>Cursor</code></li>
        </ul>
    </div>
</div>

{#if copyMessage}
    <div class="copy-message">
        {copyMessage}
    </div>
{/if}

<BackButton />

<style>
    .tips-container {
        padding: 2rem;
        max-width: 800px;
        margin: 0 auto;
    }

    .content {
        background: white;
        padding: 2rem;
        border-radius: 8px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        line-height: 1.6;
    }

    h1 {
        margin-bottom: 1.5rem;
        color: #2d3748;
        font-size: 1.8rem;
    }

    ul {
        margin: 1rem 0;
        padding-left: 1.5rem;
    }

    ul ul {
        margin: 0.5rem 0;
    }

    li {
        margin: 0.5rem 0;
    }

    code {
        background: #f1f5f9;
        padding: 0.2rem 0.4rem;
        border-radius: 4px;
        font-family: monospace;
        font-size: 0.9em;
    }

    .highlight {
        color: #0ea5e9;
    }

    .code-block {
        background: #f1f5f9;
        border-radius: 6px;
        padding: 1rem;
        margin: 1rem 0;
    }

    .code-block pre {
        margin: 0;
        white-space: pre-wrap;
    }

    .code-block code {
        background: none;
        padding: 0;
    }

    .file-paths li {
        margin: 0.75rem 0;
    }

    .path-link {
        margin-top: 0.5rem;
        display: flex;
        align-items: center;
        gap: 0.5rem;
        cursor: pointer;
    }

    .path-link:hover .clickable {
        color: #2c5282;
    }

    .clickable {
        color: #3182ce;
        text-decoration: underline;
        transition: color 0.2s;
    }

    .click-hint {
        font-size: 0.8rem;
        color: #718096;
        font-style: italic;
    }

    .copy-message {
        position: fixed;
        bottom: 2rem;
        left: 50%;
        transform: translateX(-50%);
        background: #48bb78;
        color: white;
        padding: 0.75rem 1.5rem;
        border-radius: 4px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        animation: fadeIn 0.3s ease;
    }

    @keyframes fadeIn {
        from {
            opacity: 0;
            transform: translate(-50%, 1rem);
        }
        to {
            opacity: 1;
            transform: translate(-50%, 0);
        }
    }
</style> 