<script lang="ts">
    import { tooltip } from '$lib/actions/tooltip';
    import { throttle } from '$lib/helpers/functions';
    import { onMount } from 'svelte';

    let showTooltip = false;
    let container: HTMLSpanElement | null;

    onMount(onResize);

    function onResize() {
        showTooltip = container.offsetWidth < container.scrollWidth;
    }
</script>

<svelte:window on:resize={throttle(onResize, 250)} />

<span class="text u-trim" bind:this={container}>
    {#if showTooltip}
        <span
            use:tooltip={{
                content: container.innerText
            }}>
            <slot />
        </span>
    {:else}
        <span><slot /></span>
    {/if}
</span>
