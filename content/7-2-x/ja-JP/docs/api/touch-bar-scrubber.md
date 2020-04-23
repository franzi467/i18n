## クラス: TouchBarScrubber

> スクラバー (スクロールできるセレクタ) を作成します。

プロセス: [Main](../tutorial/application-architecture.md#main-and-renderer-processes)

### `new TouchBarScrubber(options)` _実験的_

* `options` Object
  * `items` [ScrubberItem[]](structures/scrubber-item.md) - このスクラバーに配置するアイテムの配列.
  * `select` Function (optional) - Called when the user taps an item that was not the last tapped item.
    * `selectedIndex` Integer - ユーザが選択したアイテムのインデックス.
  * `highlight` Function (optional) - Called when the user taps any item.
    * `highlightedIndex` Integer - ユーザがタッチしたアイテムのインデックス.
  * `selectedStyle` String (optional) - Selected item style. Defaults to `null`.
  * `overlayStyle` String (optional) - Selected overlay item style. Defaults to `null`.
  * `showArrowButtons` Boolean (任意) - 省略値は、`false` になります。
  * `mode` String (任意) - 省略値は、`free` になります。
  * `continuous` Boolean (任意) - 省略値は、`true` になります。

### インスタンスプロパティ

`TouchBarScrubber` のインスタンスには以下のプロパティがあります。

#### `touchBarScrubber.items`

このスクラバー内のアイテムを表す `ScrubberItem[]` 配列。 この値を更新すると、タッチバーのコントロールがすぐに更新されます。 この配列の要素の中のプロパティを更新しても **タッチバーは更新されません**。

#### `touchBarScrubber.selectedStyle`

スクラバー内の選択したアイテムのスタイルを表す `String`。 この値を更新すると、タッチバーのコントロールがすぐに更新されます。 取りうる値:

* `background` - `[NSScrubberSelectionStyle roundedBackgroundStyle]` に割り当て.
* `outline` - `[NSScrubberSelectionStyle outlineOverlayStyle]` に割り当て.
* `null` - ヌル文字列ではなく、本物の null で、すべてのスタイルを削除します.

#### `touchBarScrubber.overlayStyle`

スクラバー内の選択したアイテムのスタイルを表す `String`。 このスタイルは、その後ろに置かれるのではなく、スクラバーアイテムの上に重ねられます。 この値を更新すると、タッチバーのコントロールがすぐに更新されます。 以下は取りうる値です。

* `background` - `[NSScrubberSelectionStyle roundedBackgroundStyle]` に割り当て.
* `outline` - `[NSScrubberSelectionStyle outlineOverlayStyle]` に割り当て.
* `null` - ヌル文字列ではなく、本物の null で、すべてのスタイルを削除します.

#### `touchBarScrubber.showArrowButtons`

A `Boolean` representing whether to show the left / right selection arrows in this scrubber. Updating this value immediately updates the control in the touch bar.

#### `touchBarScrubber.mode`

A `String` representing the mode of this scrubber. この値を更新すると、タッチバーのコントロールがすぐに更新されます。 取りうる値:

* `fixed` - `NSScrubberModeFixed` に割り当て.
* `free` - `NSScrubberModeFree` に割り当て.

#### `touchBarScrubber.continuous`

A `Boolean` representing whether this scrubber is continuous or not. この値を更新すると、タッチバーのコントロールがすぐに更新されます。