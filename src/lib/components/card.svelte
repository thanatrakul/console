<script lang="ts">
    import { clickOnEnter } from '$lib/helpers/a11y';

    export let isTile = false;
    export let isDashed = false;
    export let isButton = false;
    export let danger = false;
    export let href: string = null;

    function getElement(): string {
        switch (true) {
            case !!href:
                return 'a';
            case isButton:
                return 'button';
            default:
                return 'article';
        }
    }
</script>

<svelte:element
    this={getElement()}
    class="card"
    class:common-section={!isTile}
    class:is-border-dashed={isDashed}
    class:is-danger={danger}
    on:click
    on:keyup={clickOnEnter}
    {href}>
    <slot />
</svelte:element>
