# Filament: a modal without an id closed twice stays over the page

A minimal reproduction, built from the [Filament issue reproduction template](https://github.com/filamentphp/issue-reproduction-template) (branch `5.x`, Filament v5.11.4).

The only code added to the template is `app/Filament/Resources/Users/UserResource.php`: a table with one filter and `->filtersLayout(FiltersLayout::Modal)`.

## Setup

```bash
git clone https://github.com/adamaxa/filament-modal-double-close.git
cd filament-modal-double-close
composer setup
php artisan serve
```

Log in at <http://127.0.0.1:8000/admin> with `test@example.com` / `password`.

## Steps

1. Open <http://127.0.0.1:8000/admin/users>.
2. Press the filter button of the table. The filters open in a modal.
3. Close the modal twice before it has gone, in any of these ways:
   - press Escape twice in quick succession;
   - press Escape and click next to the modal at the same moment;
   - or, to make it certain, run this in the console while the modal is open:

     ```js
     for (const press of [1, 2]) document.dispatchEvent(new KeyboardEvent('keydown', { key: 'Escape', bubbles: true }))
     ```

## Result

- The console shows `Uncaught TypeError: Cannot read properties of null (reading 'focus')`.
- The modal looks closed, but its root element is never hidden (`display` stays `block`). The transparent `.fi-modal-window-ctn` covers the whole page and takes every click, so nothing on the page can be pressed until it is reloaded.

One Escape, or two Escapes 100 ms apart on a fast machine, close the modal as expected. The window for the second close is the time the browser needs to draw the first two frames of the leave transition, so it grows with a busy page or a slow machine.
