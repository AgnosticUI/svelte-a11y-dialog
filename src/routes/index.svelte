<script>
  import { SvelteA11yDialog } from "$lib";

  let dialogInstance;

  const assignDialogInstance = (ev) => {
    dialogInstance = ev.detail.instance;
  };

  // const openDialog = () => console.log('openDialog called...')
  const openDialog = () => {
    if (dialogInstance) {
      dialogInstance.show();
    }
  };
</script>
<div id="dialog-root" />
<h1>Dialog Test</h1>
<p>The following opens because we've assigned a dialog <code>ref</code>:</p>
<button
  type="button"
  data-test-id="dialogRefBtn"
  on:click={openDialog}
>
  Open dialog via dialogRef
</button>
<p>The following opens because a11y-dialog uses the <code>data-a11y-dialog-show</code> data attribute:</p>
<button
  type="button"
  data-test-id="dataA11yBtn"
  data-a11y-dialog-show="a11y-dialog"
>
  Open the dialog via data attribute
</button>
<SvelteA11yDialog 
  id="a11y-dialog"
  dialogRoot="#dialog-root"
  closeButtonLabel="My close button label"
  closeButtonPosition="last"
  titleId="uniqueTitleId"
  role="dialog"
  on:instance={assignDialogInstance}
>
  <svelte:fragment slot="closeButtonContent">
    <span>Close</span>
  </svelte:fragment> 
  <svelte:fragment slot="title">
    <span data-test-id="dialogTitle">A11yDialog Test</span>
  </svelte:fragment> 
  <div>
    <p>This is some content</p>
  </div>
</SvelteA11yDialog>

<style global lang="css">

/* Just some needless styles for fun */
body {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.dialog-container {
  display: flex;
  z-index: 2;
}

.dialog-overlay {
  background-color: rgba(43, 46, 56, 0.9);
  animation: fade-in 200ms both;
}

.dialog-overlay,
.dialog-container {
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
}

/* Crucial—dialog w/not hide visually without this rule */
.dialog-container[aria-hidden='true'] {
  display: none;
}

.dialog-content {
  background-color: rgb(255, 255, 255);
  margin: auto;
  z-index: 2;
  position: relative;
  padding: 1em;
  max-width: 90%;
  width: 600px;
  border-radius: 2px;
}

</style>