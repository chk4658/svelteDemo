<script lang="ts">
    import BackButton from '$lib/components/BackButton.svelte';
    import { onMount } from 'svelte';

    let imageFile: File | null = null;
    let imageUrl: string = '';
    let originalWidth: number = 0;
    let originalHeight: number = 0;
    let width: number = 0;
    let height: number = 0;
    let keepAspectRatio: boolean = true;
    let aspectRatio: number = 1;
    let resultImageUrl: string = '';

    function handleFileSelect(event: Event) {
        const input = event.target as HTMLInputElement;
        if (input.files && input.files[0]) {
            imageFile = input.files[0];
            const reader = new FileReader();
            reader.onload = (e) => {
                if (e.target?.result) {
                    imageUrl = e.target.result as string;
                    const img = new Image();
                    img.onload = () => {
                        originalWidth = img.width;
                        originalHeight = img.height;
                        aspectRatio = originalWidth / originalHeight;
                        width = originalWidth;
                        height = originalHeight;
                    };
                    img.src = imageUrl;
                }
            };
            reader.readAsDataURL(imageFile);
        }
    }

    function handleWidthChange() {
        if (keepAspectRatio && width > 0) {
            height = Math.round(width / aspectRatio);
        }
    }

    function handleHeightChange() {
        if (keepAspectRatio && height > 0) {
            width = Math.round(height * aspectRatio);
        }
    }

    function handleAspectRatioToggle() {
        if (keepAspectRatio) {
            width = originalWidth;
            height = originalHeight;
        }
    }

    async function resizeImage() {
        if (!imageUrl || width <= 0 || height <= 0) return;

        const canvas = document.createElement('canvas');
        canvas.width = width;
        canvas.height = height;
        const ctx = canvas.getContext('2d');
        
        if (!ctx) return;

        const img = new Image();
        img.src = imageUrl;
        
        await new Promise(resolve => {
            img.onload = () => {
                ctx.drawImage(img, 0, 0, width, height);
                resultImageUrl = canvas.toDataURL(imageFile?.type || 'image/png');
                resolve(null);
            };
        });
    }

    function downloadImage() {
        if (!resultImageUrl) return;
        
        const link = document.createElement('a');
        link.download = `resized_${imageFile?.name || 'image'}`;
        link.href = resultImageUrl;
        link.click();
    }
</script>

<div class="container">
    <h1>图片尺寸调整</h1>

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

    {#if imageUrl}
        <div class="image-info">
            <div class="preview">
                <img src={imageUrl} alt="预览" class="source-image"/>
                {#if resultImageUrl}
                    <img src={resultImageUrl} alt="调整后" class="result-image"/>
                {/if}
            </div>
            
            <div class="dimensions">
                <div class="original-size">
                    原始尺寸: {originalWidth} x {originalHeight}
                </div>
                
                <div class="inputs">
                    <div class="input-group">
                        <label for="width">宽度:</label>
                        <input
                            type="number"
                            id="width"
                            bind:value={width}
                            on:input={handleWidthChange}
                            min="1"
                        />
                    </div>
                    
                    <div class="input-group">
                        <label for="height">高度:</label>
                        <input
                            type="number"
                            id="height"
                            bind:value={height}
                            on:input={handleHeightChange}
                            min="1"
                        />
                    </div>
                    
                    <div class="checkbox-group">
                        <label>
                            <input
                                type="checkbox"
                                bind:checked={keepAspectRatio}
                                on:change={handleAspectRatioToggle}
                            />
                            保持宽高比
                        </label>
                    </div>
                </div>

                <div class="actions">
                    <button class="resize-button" on:click={resizeImage}>调整尺寸</button>
                    {#if resultImageUrl}
                        <button class="download-button" on:click={downloadImage}>下载图片</button>
                    {/if}
                </div>
            </div>
        </div>
    {/if}
</div>

<BackButton />

<style>
    .container {
        max-width: 800px;
        margin: 0 auto;
        padding: 2rem;
    }

    h1 {
        color: #2d3748;
        margin-bottom: 2rem;
        text-align: center;
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

    .image-info {
        background: white;
        padding: 2rem;
        border-radius: 8px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    }

    .preview {
        display: flex;
        gap: 2rem;
        margin-bottom: 2rem;
        justify-content: center;
    }

    .source-image, .result-image {
        max-width: 300px;
        max-height: 300px;
        object-fit: contain;
    }

    .dimensions {
        text-align: center;
    }

    .original-size {
        margin-bottom: 1rem;
        color: #4a5568;
    }

    .inputs {
        display: flex;
        gap: 2rem;
        justify-content: center;
        align-items: center;
        margin-bottom: 1.5rem;
    }

    .input-group {
        display: flex;
        align-items: center;
        gap: 0.5rem;
    }

    .input-group input[type="number"] {
        width: 100px;
        padding: 0.5rem;
        border: 1px solid #cbd5e0;
        border-radius: 4px;
    }

    .checkbox-group {
        display: flex;
        align-items: center;
        gap: 0.5rem;
    }

    .actions {
        display: flex;
        gap: 1rem;
        justify-content: center;
    }

    .resize-button, .download-button {
        padding: 0.75rem 1.5rem;
        border: none;
        border-radius: 6px;
        cursor: pointer;
        transition: all 0.2s;
    }

    .resize-button {
        background: #4a5568;
        color: white;
    }

    .download-button {
        background: #48bb78;
        color: white;
    }

    .resize-button:hover, .download-button:hover {
        transform: translateY(-2px);
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    }

    .resize-button:hover {
        background: #2d3748;
    }

    .download-button:hover {
        background: #38a169;
    }
</style> 