<script lang="ts">
    import { goto } from '$app/navigation';
    import { base } from '$app/paths';
    import { page } from '$app/stores';
    import { trackEvent } from '$lib/actions/analytics';
    import { Modal } from '$lib/components';
    import { Button } from '$lib/elements/forms';
    import { addNotification } from '$lib/stores/notifications';
    import { sdkForProject } from '$lib/stores/sdk';

    export let showDelete = false;
    const functionId = $page.params.function;

    const handleSubmit = async () => {
        try {
            await sdkForProject.functions.delete(functionId);
            showDelete = false;
            addNotification({
                type: 'success',
                message: `Function has been deleted`
            });
            await goto(`${base}/console/project-${$page.params.project}/functions`);
            trackEvent('submit_function_delete');
        } catch (error) {
            addNotification({
                type: 'error',
                message: error.message
            });
        }
    };
</script>

<Modal bind:show={showDelete} on:submit={handleSubmit} warning>
    <svelte:fragment slot="header">Delete Function</svelte:fragment>
    <p>
        Are you sure you want to delete this function and all associated deployments from your
        project?
    </p>
    <svelte:fragment slot="footer">
        <Button text on:click={() => (showDelete = false)}>Cancel</Button>
        <Button secondary submit>Delete</Button>
    </svelte:fragment>
</Modal>
