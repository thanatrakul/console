<script lang="ts">
    import { goto } from '$app/navigation';
    import { trackEvent } from '$lib/actions/analytics';
    import { Modal, CustomId } from '$lib/components';
    import { Pill } from '$lib/elements';
    import { InputText, Button, FormList } from '$lib/elements/forms';
    import { addNotification } from '$lib/stores/notifications';
    import { sdkForConsole } from '$lib/stores/sdk';
    import { createEventDispatcher } from 'svelte';

    export let show = false;
    export let teamId: string;

    const dispatch = createEventDispatcher();

    let id: string;
    let name: string;
    let showCustomId = false;
    let error: string;
    let isCreating = false;

    async function create() {
        try {
            isCreating = true;
            const project = await sdkForConsole.projects.create(
                id ?? 'unique()',
                name,
                teamId,
                'default'
            );
            dispatch('created', project);
            trackEvent('submit_project_create');
            addNotification({
                type: 'success',
                message: `${name} has been created`
            });
            await goto(`/console/project-${project.$id}`);
        } catch ({ message }) {
            isCreating = false;
            error = message;
        }
    }
</script>

<Modal {error} on:submit={create} size="big" bind:show>
    <svelte:fragment slot="header">Create Project</svelte:fragment>
    <FormList>
        <InputText
            id="name"
            label="Name"
            bind:value={name}
            required
            autofocus={true}
            disabled={isCreating} />
        {#if !showCustomId}
            <div>
                <Pill button on:click={() => (showCustomId = !showCustomId)}>
                    <span class="icon-pencil" aria-hidden="true" /><span class="text">
                        Project ID
                    </span>
                </Pill>
            </div>
        {:else}
            <CustomId bind:show={showCustomId} name="Project" bind:id />
        {/if}
    </FormList>
    <svelte:fragment slot="footer">
        <Button secondary on:click={() => (show = false)}>Cancel</Button>
        <Button submit disabled={isCreating}>Create</Button>
    </svelte:fragment>
</Modal>
