## Kelas: TouchBarPopover

> Membuat sebuah popover pada palang sentuh (touch bar) untuk aplikasi asli macOs

Proses: [Main](../glossary.md#main-process)

### `new TouchBarPopover(options)`

* `options` Object
  * `label` String (tidak wajib diisi) - tombol teks Popover.
  * `ikon` [GambarAsli](native-image.md) (tidak wajib diisi) - tombol ikon Popover.
  * `items` [TouchBar](touch-bar.md) - Items to display in the popover.
  * `showCloseButton` Boolean (optional) - `true` to display a close button on the left of the popover, `false` to not show it. Defaultnya adalah `true`.

### Instance Properties

Properti berikut ini tersedia dalam kejadian `TouchBarPopover`:

#### `touchBarPopover.label`

A `String` representing the popover's current button text. Changing this value immediately updates the popover in the touch bar.

#### `touchBarPopover.icon`

A `NativeImage` representing the popover's current button icon. Changing this value immediately updates the popover in the touch bar.