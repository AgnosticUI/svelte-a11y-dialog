# Svelte a11y-dialog

This is a Svelte wrapper component for [`a11y-dialog@7.3.0`](https://github.com/KittyGiraudel/a11y-dialog)

- [Install](#install)
- [Usage](#usage)
  - [Multiple dialogs](#multiple-dialogs)
- [API](#api)
- [Events](#events)
- [Slots](#slots)
- [Server-side Rendering](#server-side-rendering)

## Install

```bash
npm install svelte-a11y-dialog
```

## Usage

After installing the npm package, import the `SvelteA11yDialog` component
and optionally setup a dialog instance binding:

```js
  import { SvelteA11yDialog } from "svelte-a11y-dialog";
  let dialogInstance;
  const assignDialogInstance = (instance) => {
    dialogInstance = instance;
  }
```

Then use as follows:

```html
<button
  type="button"
  data-a11y-dialog-show="a11y-dialog"
>Open dialog</button>
<SvelteA11yDialog 
  id="a11y-dialog"
  appRoot="#svelte"
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
    <span>A11yDialog Test</span>
  </svelte:fragment> 
  <div>
    <p>This is some content</p>
  </div>
</SvelteA11yDialog>
```

In your main `index.html`, add a container where your dialog will be rendered into — `dialog-root` in this example:

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="app"></div>
    <div id="dialog-root"></div>
  </body>
</html>
```

## API

> The `a11y-dialog` documentation is [here](https://a11y-dialog.netlify.app/)

### `id`

- **Property**: `id`
- **Type**: `String`
- **Required**: `true`
- **Description**: The unique HTML `id` attribute added to the dialog element, internally used by a11y-dialog to manipulate the dialog.


### `appRoot`

- **Property**: `appRoot`
- **Type**: `String`, `Array<String>` — CSS Selector string.
- **Required**: `true`
- **Description**: The selector(s) `a11y-dialog` needs to disable when the dialog is open.

### `dialogRoot`

- **Property**: `dialogRoot`
- **Type**: `String` — CSS Selector string.
- **Required**: `true`
- **Description**: The container for the dialog to be rendered into.

### `classNames`

- **Property**: `classNames`
- **Type**: `Object`
- **Required**: `false`
- **Default**: `{}`
- **Description**: Object of classes for each HTML element of the dialog element. Keys are: `base`, `overlay`, `document`, `title`, `closeButton`. See [a11y-dialog docs](https://a11y-dialog.netlify.app/) for reference.

### `titleId`

- **Property**: `titleId`
- **Type**: `String`
- **Required**: `false`
- **Default**: Defaults to `id + '-title'`.
- **Description**: The HTML `id` attribute of the dialog’s title element, used by assistive technologies to provide context and meaning to the dialog window.

### `closeButtonLabel`

- **Property**: `closeButtonLabel`
- **Type**: `String`
- **Required**: `false`
- **Default**: `'Close this dialog window'`
- **Description**: The HTML `aria-label` attribute of the close button, used by assistive technologies to provide extra meaning to the usual cross-mark.

### `role`

- **Property**: `role`
- **Type**: `String`
- **Required**: `false`
- **Default**: `dialog`
- **Description**: The `role` attribute of the dialog element, either `dialog` (default) or `alertdialog` to make it a modal (preventing closing on click outside of <kbd>ESC</kbd> key).

## Events

### `on:instance`

- **Returns**: An `a11y-dialog` instance when a11y-dialog is instantiated via
a Svelte `dispatch` event. Note that as is idiomatic in dispatched Svelte events, you need to access the instance via `event.detail` object (see below).
- **Description**: This event emits the `a11y-dialog` instance once the component has been instantiated. Here's an example of how to set up in your parent component (pay special note of the `ev.detail.instance` to gain access to the passed a11y-dialog instance!):

```svelte
<script>
  const assignDialogInstance = (ev) => {
    dialogInstance = ev.detail.instance;
  };

  const openDialog = () => {
    if (dialogInstance) {
      dialogInstance.show();
    }
  };
</script>

<SvelteA11yDialog on:instance={assignDialogInstance} ...etc
```

## Slots

### `title`

- **Name**: `title`
- **Description**: The title of the dialog, mandatory in the document to provide context to assistive technology. Could be [hidden with CSS](https://hugogiraudel.com/2016/10/13/css-hide-and-seek/) (while remaining accessible).

### `closeButtonContent`

- **Name**: `closeButtonLabel`
- **Default**: `\u00D7` (×)
- **Description**: The node that is the inner HTML of the close button.

### `closeButtonPosition`

- **Name**: `closeButtonPosition`
- **Default**: `first`
- **Description**: One of `first`, `last`, or `none`

## Server-side Rendering

This has only been tested client-side.
