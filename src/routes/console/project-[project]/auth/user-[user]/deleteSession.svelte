<script lang="ts">
    import { invalidate } from '$app/navigation';
    import { page } from '$app/stores';
    import { trackEvent } from '$lib/actions/analytics';
    import { Modal } from '$lib/components';
    import { Dependencies } from '$lib/constants';
    import { Button } from '$lib/elements/forms';
    import { addNotification } from '$lib/stores/notifications';
    import { sdkForProject } from '$lib/stores/sdk';

    export let showDelete = false;
    export let selectedSessionId: string;

    const deleteSession = async () => {
        try {
            await sdkForProject.users.deleteSession($page.params.user, selectedSessionId);
            invalidate(Dependencies.SESSIONS);
            addNotification({
                type: 'success',
                message: 'Session has been deleted'
            });
            trackEvent('submit_session_delete');
        } catch (error) {
            addNotification({
                type: 'error',
                message: error.message
            });
        }
    };
</script>

<Modal bind:show={showDelete} on:submit={deleteSession} warning>
    <svelte:fragment slot="header">Delete Sessions</svelte:fragment>

    <p>Are you sure you want to delete this session?</p>
    <svelte:fragment slot="footer">
        <Button text on:click={() => (showDelete = false)}>Cancel</Button>
        <Button secondary submit>Delete</Button>
    </svelte:fragment>
</Modal>
