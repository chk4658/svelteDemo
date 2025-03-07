<script lang="ts">
    import BackButton from '$lib/components/BackButton.svelte';
    import {onMount} from 'svelte';
    import {slide} from 'svelte/transition';
    import * as echarts from 'echarts';

    interface DataItem {
        name: string;
        value: number;
        land?: boolean;
    }

    interface WaterfallItem {
        itemStyle?: {
            color: string;
        };
        value: number;
    }

    interface LabelItem {
        data: {
            value: number;
        };
    }

    interface LineItem {
        start: string;
        end: string;
        y: number;
    }

    let chartContainer: HTMLElement;
    let showCode: boolean = false;
    let copyMessage: string = '';
    let messageTimeout: ReturnType<typeof setTimeout>;

    const data: DataItem[] = [
        {name: 'name1', value: 3364, land: true},
        {name: 'name2', value: 1414},
        {name: 'name3', value: -1004},
        {name: 'name4', value: -176},
        {name: 'name5', value: 517},
        {name: 'name6', value: -137},
        {name: 'name7', value: -505},
        {name: 'name8', value: 3473, land: true},
    ];

    const chartCode = `interface DataItem {
    name: string;
    value: number;
    land?: boolean;
}

interface LineItem {
    start: string;
    end: string;
    y: number;
}

interface LabelItem {
    data: {
        value: number;
    };
}

const data: DataItem[] = [
    { name: 'name1', value: 3364, land: true },
    { name: 'name2', value: 1414 },
    { name: 'name3', value: -1004 },
    { name: 'name4', value: -176 },
    { name: 'name5', value: 517 },
    { name: 'name6', value: -137 },
    { name: 'name7', value: -505 },
    { name: 'name8', value: 3473, land: true },
];

function getWaterfallSeriesData(array: DataItem[]) {
    const commonData = array.map((x) => {
        if (x.land) return x.value;
        return null;
    });
    let mat = 0;
    const lineArray: LineItem[] = [];
    const matArray = array.map((x, i) => {
        if (x.land) {
            mat = x.value;
            return 0;
        }
        const currentMat = mat;
        if (i > 0) {
            lineArray.push({
                start: array[i - 1].name,
                end: x.name,
                y: currentMat
            });

            if (i !== array.length - 1 && array[i + 1].land) lineArray.push({
                start: x.name,
                end: array[i + 1].name,
                y: mat + x.value
            })
        }
        mat += x.value;
        return currentMat;
    });
    const waterFall = array.map((x) => {
        if (x.land) return null;
        return {
            itemStyle: {
                color: x.value > 0 ? "#91CC75" : "#D64550",
            },
            value: x.value,
        };
    });
    return [
        {
            name: "common",
            type: "bar",
            stack: "Ad",
            emphasis: {
                focus: "none",
            },
            data: commonData,
            itemStyle: {
                color: "#0D6ABF",
            },
            label: {
                show: true,
                position: "top",
                textStyle: {},
                formatter: (item: LabelItem) => item.data.value,
            },
        },
        {
            name: "mat",
            type: "bar",
            stack: "Ad",
            emphasis: {
                focus: "none",
            },
            data: matArray,
            itemStyle: {
                color: "transparent",
            },
            label: {
                show: false,
            },
        },
        {
            name: "waterFall",
            type: "bar",
            stack: "Ad",
            stackStrategy: "all",
            emphasis: {
                focus: "none",
            },
            data: waterFall,
            label: {
                show: true,
                position: "top",
                textStyle: {},
                formatter: (item: LabelItem) => item.data.value,
            },
            markLine: {
                show: lineArray.length !== 0,
                label: { show: false },
                lineStyle: {
                    type: "solid",
                    width: 0.5,
                },
                data: [
                    ...lineArray.map((item) => {
                        return [
                            {
                                coord: [item.start, item.y],
                                symbolSize: 0,
                            },
                            {
                                coord: [item.end, item.y],
                                symbolSize: 0,
                            },
                        ];
                    }),
                ],
            },
        },
    ];
}`;

    function getWaterfallSeriesData(array: DataItem[]) {
        const commonData = array.map((x) => {
            if (x.land) return x.value;
            return null;
        });
        let mat = 0;
        const lineArray: LineItem[] = [];
        const matArray = array.map((x, i) => {
            if (x.land) {
                mat = x.value;
                return 0;
            }
            const currentMat = mat;
            if (i > 0) {
                lineArray.push({
                    start: array[i - 1].name,
                    end: x.name,
                    y: currentMat
                });

                if (i !== array.length - 1 && array[i + 1].land) lineArray.push({
                    start: x.name,
                    end: array[i + 1].name,
                    y: mat + x.value
                })
            }
            mat += x.value;
            return currentMat;
        });
        const waterFall = array.map((x) => {
            if (x.land) return null;
            return {
                itemStyle: {
                    color: x.value > 0 ? "#91CC75" : "#D64550",
                },
                value: x.value,
            };
        });
        return [
            {
                name: "common",
                type: "bar",
                stack: "Ad",
                emphasis: {
                    focus: "none",
                },
                data: commonData,
                itemStyle: {
                    color: "#0D6ABF",
                },
                label: {
                    show: true,
                    position: "top",
                    textStyle: {},
                    formatter: (item: LabelItem) => item.data.value,
                },
            },
            {
                name: "mat",
                type: "bar",
                stack: "Ad",
                emphasis: {
                    focus: "none",
                },
                data: matArray,
                itemStyle: {
                    color: "transparent",
                },
                label: {
                    show: false,
                },
            },
            {
                name: "waterFall",
                type: "bar",
                stack: "Ad",
                stackStrategy: "all",
                emphasis: {
                    focus: "none",
                },
                data: waterFall,
                label: {
                    show: true,
                    position: "top",
                    textStyle: {},
                    formatter: (item: LabelItem) => item.data.value,
                },
                markLine: {
                    show: lineArray.length !== 0,
                    label: {show: false},
                    lineStyle: {
                        type: "solid",
                        width: 0.5,
                    },
                    data: [
                        ...lineArray.map((item) => {
                            return [
                                {
                                    coord: [item.start, item.y],
                                    symbolSize: 0,
                                },
                                {
                                    coord: [item.end, item.y],
                                    symbolSize: 0,
                                },
                            ];
                        }),
                    ],
                },
            },
        ];
    }

    function initChart() {
        if (!chartContainer) return;

        const chart = echarts.init(chartContainer);
        const option = {
            title: {
                show: false
            },
            tooltip: {
                show: false
            },
            grid: {
                left: '3%',
                right: '4%',
                bottom: '3%',
                containLabel: true
            },
            xAxis: {
                type: 'category',
                splitLine: {show: false},
                data: data.map(x => x.name)
            },
            yAxis: {
                type: 'value',
                splitLine: {show: false},
            },
            series: getWaterfallSeriesData(data)
        };

        chart.setOption(option);

        window.addEventListener('resize', () => {
            chart.resize();
        });
    }

    function showCopyMessage(text: string) {
        copyMessage = text;
        if (messageTimeout) clearTimeout(messageTimeout);
        messageTimeout = setTimeout(() => {
            copyMessage = '';
        }, 2000);
    }

    async function copyToClipboard() {
        try {
            await navigator.clipboard.writeText(chartCode);
            showCopyMessage('代码已复制到剪贴板');
        } catch (err) {
            showCopyMessage('复制失败，请重试');
        }
    }

    onMount(() => {
        initChart();
    });
</script>

<div class="container">
    <h1>ECharts 瀑布图示例</h1>

    <div class="content">
        <div class="chart-container" bind:this={chartContainer}></div>

        <div class="code-section">
            <div class="section-header">
                <div class="header-left">
                    <h2>图表代码</h2>
                    <a 
                        href="https://echarts.apache.org/zh/option.html#yAxis.splitLine"
                        target="_blank" 
                        rel="noopener noreferrer"
                        class="doc-link"
                    >
                        查看官方文档
                    </a>
                </div>
                <div class="button-group">
                    <button
                            class="toggle-button"
                            on:click={() => showCode = !showCode}
                    >
                        {showCode ? '收起' : '展开'}
                    </button>
                    <button
                            class="copy-button"
                            on:click={copyToClipboard}
                    >
                        复制代码
                    </button>
                </div>
            </div>

            {#if showCode}
                <div class="code-block" transition:slide>
                    <pre><code>{chartCode}</code></pre>
                </div>
            {/if}
        </div>
    </div>
</div>

{#if copyMessage}
    <div class="copy-message">
        {copyMessage}
    </div>
{/if}

<BackButton/>

<style>
    .container {
        max-width: 1200px;
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

    .content {
        background: white;
        padding: 2rem;
        border-radius: 8px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    }

    .chart-container {
        width: 100%;
        height: 500px;
        margin-bottom: 2rem;
    }

    .section-header {
        display: flex;
        align-items: center;
        justify-content: space-between;
        margin-bottom: 1rem;
    }

    .header-left {
        display: flex;
        align-items: center;
        gap: 1rem;
    }

    .button-group {
        display: flex;
        gap: 1rem;
    }

    .code-block {
        background: #f8f9fa;
        padding: 1rem;
        border-radius: 6px;
        overflow-x: auto;
    }

    pre {
        margin: 0;
        white-space: pre-wrap;
        font-size: 0.9rem;
        line-height: 1.5;
    }

    code {
        font-family: monospace;
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

    .doc-link {
        color: #3182ce;
        text-decoration: none;
        font-size: 0.9rem;
        transition: color 0.2s;
    }

    .doc-link:hover {
        color: #2c5282;
        text-decoration: underline;
    }
</style> 