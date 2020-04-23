## クラス: ClientRequest

> HTTP/HTTPリクエストを行います。

プロセス: [Main](../glossary.md#main-process)

`ClientRequest` は [Writable Stream](https://nodejs.org/api/stream.html#stream_writable_streams) インターフェースを実装しているため、 [EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter) です。

### `new ClientRequest(options)`

* `options` (Object | String) - If `options` is a String, it is interpreted as the request URL. If it is an object, it is expected to fully specify an HTTP request via the following properties:
  * `method` String (optional) - The HTTP request method. Defaults to the GET method.
  * `url` String (optional) - The request URL. Must be provided in the absolute form with the protocol scheme specified as http or https.
  * `session` Object (任意) - リクエストが関連付けられている [`Session`](session.md) のインスタンス。
  * `partition` String (任意) - リクエストが関連付けられている [`partition`](session.md) の名前。 省略値は、空の文字列です。 `session` オプションは、`partition` よりも優先されます。 そのため、`session` が明示的に指定されている場合、`partition` は無視されます。
  * `protocol` String (optional) - The protocol scheme in the form 'scheme:'. Currently supported values are 'http:' or 'https:'. Defaults to 'http:'.
  * `host` String (任意) - ホスト名とポート番号を連結した 'hostname:port' として指定されたサーバーホスト。
  * `hostname` String (任意) - サーバーホスト名。
  * `port` Integer (任意) - サーバーのリスニングポート番号。
  * `path` String (任意) - リクエストURLのパスの部分。
  * `redirect` String (任意) - このリクエストのリダイレクトモード。 `follow`、`error` または `manual` のいずれかにする必要があります。 省略値は、`follow` です。 モードが `error` のとき、リダイレクトは中止されます。 モードが `manual` のとき、[`request.followRedirect`](#requestfollowredirect) が呼び出されるまで、リダイレクトは遅延されます。 リダイレクトリクエストの詳細を得るため、このモードでは、[`redirect`](#event-redirect) イベントを待ち受けるようにしてください。

`protocol`、`host`、`hostname`、`port` や `path` といった `options` プロパティは、[URL](https://nodejs.org/api/url.html) モジュールで説明されている Node.js モデルに厳密に従うようにしてください。

例えば、以下のようにすると、'github.com' に対してリクエストをしているのと同じです。

```JavaScript
const request = net.request({
  method: 'GET',
  protocol: 'https:',
  hostname: 'github.com',
  port: 443,
  path: '/'
})
```

### インスタンスイベント

#### イベント: 'response'

戻り値:

* `response` IncomingMessage - HTTPレスポンスメッセージを表すオブジェクト。

#### イベント: 'login'

戻り値：

* `authInfo` Object
  * `isProxy` Boolean
  * `scheme` String
  * `host` String
  * `port` Integer
  * `realm` String
* `callback` Function
  * `username` String
  * `password` String

認証プロキシがユーザの資格情報を要求しているときに発生します。

`callback` ファンクションは、ユーザの資格情報と共にコールバックされます。

* `username` String
* `password` String

```JavaScript
request.on('login', (authInfo, callback) => {
  callback('username', 'password')
})
```
空の資格情報を指定すると、リクエストがキャンセルされ、レスポンスオブジェクトで認証エラーが返ります。

```JavaScript
request.on('response', (response) => {
  console.log(`STATUS: ${response.statusCode}`);
  response.on('error', (error) => {
    console.log(`ERROR: ${JSON.stringify(error)}`)
  })
})
request.on('login', (authInfo, callback) => {
  callback()
})
```

#### イベント: 'finish'

`request` のデータの最後のチャンクが `request` オブジェクトに書き込まれた直後に発生します。

#### イベント: 'abort'

Emitted when the `request` is aborted. The `abort` event will not be fired if the `request` is already closed.

#### イベント: 'error'

戻り値:

* `error` Error - 失敗に関するいくつかの情報を提供するエラーオブジェクト。

`net` モジュールがネットワークリクエストを行うのに失敗するときに発生します。 通常、`request` オブジェクトが `error` イベントを発生させるとき、続いて `close` イベントが発生し、レスポンスオブジェクトが返ることはありません。

#### イベント: 'close'

HTTPのリクエストからレスポンスまでのやり取りの最後のイベントして発生します。 `close` イベントは、`request` または `response` オブジェクトのいずれでもこれ以上のイベントが発生しないことを示します。


#### イベント: 'redirect'

戻り値:

* `statusCode` Integer
* `method` String
* `redirectUrl` String
* `responseHeaders` Object

Emitted when there is redirection and the mode is `manual`. Calling [`request.followRedirect`](#requestfollowredirect) will continue with the redirection.

### インスタンスプロパティ

#### `request.chunkedEncoding`

リクエストがHTTPのチャンク形式転送エンコーディングを使用するかどうかを指定する `Boolean` 型。 省略値は、false です。 プロパティは読み書き可能ですが、HTTPヘッダーがまだ送信されていない最初の書き込み操作の前でしか設定できません。 最初の書き込みの後、`chunkedEncoding` プロパティを設定しようとすると、エラーがスローされます。

Electronのプロセスメモリの中で内部的にバッファする代わりにデータが細切れにストリーミングされるため、大きなリクエストボディを送信する必要がある場合、チャンク形式のエンコーディングを使用することを強く推奨します。

### インスタンスメソッド

#### `request.setHeader(name, value)`

* `name` String - 追加する HTTP ヘッダーの名前。
* `value` Object - 追加するHTTPヘッダーの値。

別の HTTP ヘッダーを追加します。 ヘッダー名は小文字にされることなく、そのまま出力されます。 最初の書き込み前のみ呼び出すことができます。 最初の書き込み後にこのメソッドを呼び出すとエラーがスローされます。 渡された値が `String` 型でない場合、最終的な値を得るために `toString()` メソッドが呼び出されます。

#### `request.getHeader(name)`

* `name` String - 追加したヘッダーの名前を指定します。

戻り値 `Object` - 先に設定した追加したヘッダーの名前の値。

#### `request.removeHeader(name)`

* `name` String - 追加したヘッダーの名前を指定します。

Removes a previously set extra header name. This method can be called only before first write. Trying to call it after the first write will throw an error.

#### `request.write(chunk[, encoding][, callback])`

* `chunk` (String | Buffer) - A chunk of the request body's data. If it is a string, it is converted into a Buffer using the specified encoding.
* `encoding` String (optional) - Used to convert string chunks into Buffer objects. Defaults to 'utf-8'.
* `callback` Function (任意) - 書き込み操作の終了後に呼び出されます。

`callback` は、Node.jsのAPIとの類似性を維持する目的で導入された本質的にはダミーのファンクションです。 `chunk` コンテンツがChromiumのネットワークレイヤーに到達した後、すぐに非同期で呼び出されます。 Node.jsの実装とは違って、`callback` が呼び出される前に `chunk` コンテンツが書き込まれていることは保証されません。

リクエストボディにデータのチャンクを追加します。 最初の書き込み操作では、リクエストヘッダーも出力される可能性があります。 最初の書き込み操作の後、カスタムヘッダーを追加したり、削除したりすることはできません。

#### `request.end([chunk][, encoding][, callback])`

* `chunk` (String | Buffer) (任意)
* `encoding` String (任意)
* `callback` Function (任意)

Sends the last chunk of the request data. Subsequent write or end operations will not be allowed. The `finish` event is emitted just after the end operation.

#### `request.abort()`

現在進行中のHTTPトランザクションをキャンセルします。 リクエストで既に `close` イベントが発生していた場合、中止操作は無効になります。 そうでない場合、進行中のイベントでは、`abort` と `close` イベントが発生します。 さらに、処理中のレスポンスオブジェクトがある場合、`aborted` イベントが発生します。

#### `request.followRedirect()`

リダイレクトモードが、`manual` のとき、遅延しているリダイレクトリクエストを続行します。

#### `request.getUploadProgress()`

戻り値 `Object`:

* `active` Boolean - Whether the request is currently active. If this is false no other properties will be set
* `started` Boolean - Whether the upload has started. If this is false both `current` and `total` will be set to 0.
* `current` Integer - どのくらいアップロードしたかのバイト数。
* `total` Integer - このリクエストでアップロードされるバイト数。

このメソッドを `POST` リクエストと組み合わせて使用すると、ファイルのアップロードや他のデータ転送の進行状況を取得できます。