<script lang="ts">
    import BackButton from '$lib/components/BackButton.svelte';
    import { onMount } from 'svelte';
    import { slide } from 'svelte/transition';

    let imageFile: File | null = null;
    let base64String: string = '';
    let splitLength: number = 100;
    let splitArray: string[] = [];
    let copyMessage: string = '';
    let messageTimeout: ReturnType<typeof setTimeout>;
    let showBase64: boolean = false;
    let showArray: boolean = false;

    function handleFileSelect(event: Event) {
        const input = event.target as HTMLInputElement;
        if (input.files && input.files[0]) {
            imageFile = input.files[0];
            const reader = new FileReader();
            reader.onload = (e) => {
                if (e.target?.result) {
                    base64String = e.target.result as string;
                    splitBase64();
                }
            };
            reader.readAsDataURL(imageFile);
        }
    }

    function splitBase64() {
        if (!base64String || splitLength <= 0) return;
        splitArray = [];
        for (let i = 0; i < base64String.length; i += splitLength) {
            splitArray.push(base64String.slice(i, i + splitLength));
        }
    }

    function showCopyMessage(text: string) {
        copyMessage = text;
        if (messageTimeout) clearTimeout(messageTimeout);
        messageTimeout = setTimeout(() => {
            copyMessage = '';
        }, 2000);
    }

    async function copyToClipboard(text: string, message: string) {
        try {
            await navigator.clipboard.writeText(text);
            showCopyMessage(message);
        } catch (err) {
            showCopyMessage('复制失败，请重试');
        }
    }

    async function copyArray() {
        const arrayString = JSON.stringify(splitArray, null, 2);
        await copyToClipboard(arrayString, '数组已复制到剪贴板');
    }

    $: {
        if (splitLength) splitBase64();
    }
</script>

<div class="container">
    <h1>图片转Base64</h1>

    <div class="upload-section">
        <label class="upload-button" for="imageInput">
            选择图片
            <input
                type="file"
                id="imageInput"
                accept="image/*"
                on:change={handleFileSelect}
                class="hidden"
            />
        </label>
    </div>

    {#if base64String}
        <div class="content">
            <div class="preview">
                <img src={base64String} alt="预览" />
            </div>

            <div class="base64-section">
                <div class="section-header">
                    <h2>Base64字符串</h2>
                    <button 
                        class="toggle-button"
                        on:click={() => showBase64 = !showBase64}
                    >
                        {showBase64 ? '隐藏' : '显示'}
                    </button>
                    <button 
                        class="copy-button"
                        on:click={() => copyToClipboard(base64String, 'Base64已复制到剪贴板')}
                    >
                        复制完整字符串
                    </button>
                </div>
                {#if showBase64}
                    <div class="base64-text" transition:slide>
                        <pre>{base64String}</pre>
                    </div>
                {/if}
            </div>

            <div class="split-section">
                <div class="split-header">
                    <h2>分割设置</h2>
                    <div class="split-input">
                        <label for="splitLength">每段长度：</label>
                        <input
                            type="number"
                            id="splitLength"
                            bind:value={splitLength}
                            min="1"
                        />
                    </div>
                    <button 
                        class="toggle-button"
                        on:click={() => showArray = !showArray}
                    >
                        {showArray ? '隐藏' : '显示'}
                    </button>
                    <button 
                        class="copy-button"
                        on:click={copyArray}
                    >
                        复制数组
                    </button>
                </div>
                {#if showArray}
                    <div class="split-result" transition:slide>
                        <pre>{JSON.stringify(splitArray, null, 2)}</pre>
                    </div>
                {/if}
            </div>
        </div>
    {/if}

    {#if copyMessage}
        <div class="copy-message">
            {copyMessage}
        </div>
    {/if}
</div>

<BackButton />

<style>
    .container {
        max-width: 1000px;
        margin: 0 auto;
        padding: 2rem;
    }

    h1 {
        color: #2d3748;
        margin-bottom: 2rem;
        text-align: center;
        font-size: 1.8rem;
    }

    h2 {
        color: #2d3748;
        margin: 0;
        font-size: 1.2rem;
    }

    .upload-section {
        text-align: center;
        margin-bottom: 2rem;
    }

    .upload-button {
        display: inline-block;
        padding: 1rem 2rem;
        background: #4a5568;
        color: white;
        border-radius: 8px;
        cursor: pointer;
        transition: background-color 0.2s;
    }

    .upload-button:hover {
        background: #2d3748;
    }

    .hidden {
        display: none;
    }

    .content {
        background: white;
        padding: 2rem;
        border-radius: 8px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    }

    .preview {
        text-align: center;
        margin-bottom: 2rem;
    }

    .preview img {
        max-width: 300px;
        max-height: 300px;
        object-fit: contain;
    }

    .section-header, .split-header {
        display: flex;
        align-items: center;
        gap: 1rem;
        margin-bottom: 1rem;
    }

    .base64-text, .split-result {
        background: #f8f9fa;
        padding: 1rem;
        border-radius: 6px;
        overflow-x: auto;
        margin-bottom: 2rem;
    }

    pre {
        margin: 0;
        white-space: pre-wrap;
        word-break: break-all;
        font-size: 0.9rem;
        line-height: 1.5;
    }

    .copy-button, .toggle-button {
        padding: 0.5rem 1rem;
        border: none;
        border-radius: 4px;
        cursor: pointer;
        transition: all 0.2s;
        font-size: 0.9rem;
    }

    .copy-button {
        background: #4a5568;
        color: white;
    }

    .toggle-button {
        background: #e2e8f0;
        color: #4a5568;
    }

    .copy-button:hover, .toggle-button:hover {
        transform: translateY(-1px);
    }

    .copy-button:hover {
        background: #2d3748;
    }

    .toggle-button:hover {
        background: #cbd5e0;
    }

    .split-input {
        display: flex;
        align-items: center;
        gap: 0.5rem;
    }

    .split-input input {
        width: 100px;
        padding: 0.4rem;
        border: 1px solid #cbd5e0;
        border-radius: 4px;
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