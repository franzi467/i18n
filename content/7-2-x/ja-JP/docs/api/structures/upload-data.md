# UploadData オブジェクト

* `bytes` Buffer - 送信されるコンテンツ。
* `file` String (任意) - アップロードされるファイルのパス。
* `blobUUID` String (optional) - UUID of blob data. データを取得するには、[ses.getBlobData](../session.md#sesgetblobdataidentifier) メソッドを使用します。