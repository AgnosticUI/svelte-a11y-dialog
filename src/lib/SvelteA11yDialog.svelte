<script lang="ts">
  import { createEventDispatcher, onMount, onDestroy, tick } from "svelte";
  import A11yDialog from 'a11y-dialog';

  const dispatch = createEventDispatcher();

  export let id: string;
  export let titleId = '';
  export let role: 'dialog' | 'alertdialog' = 'dialog';
  export let dialogRoot: string;
  export let closeButtonLabel = 'Close this dialog window';
  export let closeButtonPosition: 'first' | 'last' | 'none' = 'first';
  export let classNames = {}
  const defaultClassNames = {
    container: 'dialog-container',
    document: 'dialog-content',
    overlay: 'dialog-overlay',
    element: 'dialog-element',
    title: 'dialog-title',
    closeButton: 'dialog-close',
  };
  const classes = {...defaultClassNames, ...classNames};

  // The dialog instance
  let dialog;

  // Dialog element's binding
  let rootElement;

  const portalTarget: string = dialogRoot || "document.body";

  const fullTitleId = titleId || `${id}-title`;

  const roleAttribute = ['dialog', 'alertdialog'].includes(role)
    ? role
    : 'dialog';

  // In case of SSR we don't render element until hydration is complete
  let mounted = false;
  onMount(() => mounted = true);

  onDestroy(() => {
    if (dialog) {
      dialog.destroy();
    }
  });

  const instantiateDialog = async () => {
    await tick();
    dialog = new A11yDialog(rootElement, portalTarget);
    dispatch("instance", {
      "instance": dialog
    });
  };

  const teleportNode = async (node) => {
    const destination = document.querySelector(portalTarget);
    destination.appendChild(node);
    // We don't render the template until mounted. So we need
    // wait one more "tick" before instantiating the dialog
    instantiateDialog();
  }

  /**
   * Svelte actions don't want to be async so this is a hack
   * to get around that by delegating to teleportNode
   */
  const teleport = (node) => {
    teleportNode(node);
  }

  const close = () => {
    dialog.hide();
  }
</script>

{#if mounted}
  <div
    id={id}
    bind:this={rootElement}
    class={classes.container}
    role={roleAttribute}
    aria-hidden="true"
    aria-labelledby={fullTitleId}
    use:teleport
  >
      <div
        data-a11y-dialog-hide
        tabindex="-1"
        class={classes.overlay}
        on:click|preventDefault={role === 'alertdialog' ? undefined : close}
      ></div>
      <div role="document" class={classes.document}>
        {#if closeButtonPosition === 'first'}
          <button
            data-a11y-dialog-hide
            type="button"
            class={classes.closeButton}
            aria-label={closeButtonLabel}
            on:click={close}
          >
            <slot name="closeButtonContent">
              { '\u00D7' }
            </slot>
          </button>
        {/if}
        <p id={fullTitleId} class={classes.title}>
          <slot name="title" />
        </p>
        <slot />
        {#if closeButtonPosition === 'last'}
          <button
            data-a11y-dialog-hide
            type="button"
            class={classes.closeButton}
            aria-label={closeButtonLabel}
            on:click={close}
          >
            <slot name="closeButtonContent">
              { '\u00D7' }
            </slot>
          </button>
        {/if}
      </div>
    </div>
{/if}
