<script lang="ts">
    import { goto, invalidate } from '$app/navigation';
    import { base } from '$app/paths';
    import { page } from '$app/stores';
    import { Modal } from '$lib/components';
    import { Button } from '$lib/elements/forms';
    import { addNotification } from '$lib/stores/notifications';
    import { collection } from '../store';
    import type { Attributes } from '../store';
    import { sdkForProject } from '$lib/stores/sdk';
    import { Dependencies } from '$lib/constants';
    import { trackEvent } from '$lib/actions/analytics';

    export let showDelete = false;
    export let selectedAttribute: Attributes;
    const databaseId = $page.params.database;

    const handleDelete = async () => {
        try {
            await sdkForProject.databases.deleteAttribute(
                databaseId,
                $collection.$id,
                selectedAttribute.key
            );
            invalidate(Dependencies.COLLECTION);
            showDelete = false;
            addNotification({
                type: 'success',
                message: `Attribute has been deleted`
            });
            trackEvent('submit_attribute_delete');
            await goto(
                `${base}/console/project-${$page.params.project}/databases/database-${databaseId}/collection-${$page.params.collection}/attributes`
            );
        } catch (error) {
            addNotification({
                type: 'error',
                message: error.message
            });
        }
    };
</script>

<Modal warning={true} bind:show={showDelete} on:submit={handleDelete}>
    <svelte:fragment slot="header">Delete Attribute</svelte:fragment>
    <p>
        Are you sure you want to delete <b>'{selectedAttribute?.key}' from {$collection?.name}</b>?
    </p>
    <svelte:fragment slot="footer">
        <Button text on:click={() => (showDelete = false)}>Cancel</Button>
        <Button secondary submit>Delete</Button>
    </svelte:fragment>
</Modal>
