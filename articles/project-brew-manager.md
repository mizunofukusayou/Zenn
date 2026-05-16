---
title: "brewManagerプロジェクトで学習したことのまとめ"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [go, SQLite]
published: false
---

# Go
## package管理
goにはパッケージマネージャーが標準で備わっている。
|役割|Node.js|Go|
|-|-|-|
|初期化|npm init|go mod init [プロジェクト名]|
|ライブラリの追加|npm install [ライブラリ名]|go get [ライブラリ名]|
|設定ファイル|package.json|go.mod|
|ロックファイル|package-lock.json|go.sum|

## Error処理
main関数で、`log.Printf`を使ってログを出力する。
逆に、main関数以外では、ログを出さず、`fmt.Errorf`でエラーをラップ(`%w`)して、呼び出し元に返す。
呼び出しもとは、`errors.Is, errors.As`を使ってエラーの種類を判定する。

### errors.Is, errors.Asの使い分け
- `errors.Is`: エラーが特定のエラーと等しいかどうかを判定する。エラーがラップされている場合でも、元のエラーと比較できる。
- `errors.As`: エラーが特定の型にキャストできるかどうかを判定する。エラーがラップされている場合でも、元のエラーの型を判定できる。(第一引数に`err`, 第二引数に、ポインタを渡すことで、エラーの型にキャストする。)
基本的には、`errors.Is`を使い、詳細を伝えたい場合には`errors.As`を使う。

例
```go
// パターンA：Is (スッキリ)
if errors.Is(err, strconv.ErrSyntax) {
    // 「文法エラーだな」と直感的にわかる
    return "invalid number format"
}

// パターンB：As (ちょっと重い)
var numErr *strconv.NumError
if errors.As(err, &numErr) {
    // 「詳細が必要なんだな」と読み手は構える
    return fmt.Sprintf("invalid input: %s", numErr.Num)
}
```

### main関数でのエラー出力に関して
User Error
- `fmt.Fprintf(os.Stderr, "エラーメッセージ")`で、標準エラー出力にエラーメッセージを出力する。
これを使用すると、標準エラー出力に、エラーメッセージだけを出力する。
`log.Printf`を使用すると、標準エラー出力に、エラーメッセージと、日付と時間が出力される。
`fmt.Printf`は、標準出力に出力するため、CLIだとパイプで繋げた時に困る。

System Error
- `log.Printf`は小規模なプロジェクト用
- `log/slog`は大規模なプロジェクト用（ログのレベル分けや、ファイル出力などができる）
- `log.Fatalf`は、エラーを出力した後に、プログラムを強制終了させる。(deferも実行されない)(`log.Printf + os.Exit(1)`と同等)

### CLIにおける終了コード
|終了ステータス|意味|具体的な使いどころ|
|-|-|-|
|0|Success|すべての処理が正常に完了した。|
|1|General Error|データベース接続失敗、SQL実行エラー、ファイル読み込み失敗など。|
|2|Usage Error|必須引数の不足、未知のコマンド、不正なオプション指定など。|

## mux
`mux := http.NewServeMux()`で、HTTPリクエストの転送先リストを作成する。
`mux.HandleFunc("/path", handler)`で、pathにアクセスがあったらhandler関数を呼び出すように設定する。
`http.ListenAndServe(":8080", mux)`で、サーバーを起動する。
↑このままだと、スローロリス攻撃に弱いので、`http.Server`を使って、タイムアウトを設定する。
```go
server := &http.Server{
    Addr:         ":8080",
    Handler:      mux,
    ReadTimeout:  5 * time.Second, // クライアントからのリクエストの読み込みにかかる最大時間
    WriteTimeout: 10 * time.Second, // クライアントへのレスポンスの書き込みにかかる最大時間
    IdleTimeout:  120 * time.Second, // クライアントとの接続がアイドル状態のときの最大時間
}
err := server.ListenAndServe()
if err != nil {
    log.Fatalf("サーバーの起動に失敗しました: %v", err)
}
```


## apiのレスポンス
### レスポンスの書き方
流れは、
1. 属性（ヘッダー）の設定
2. 結果（ステータスコード）の設定 (省略可能、省略すると200番)
3. 中身（ボディ）の書き込み
```go
w.Header().Set("Content-Type", "application/json") // JSON形式でレスポンスを返すことを明示する
w.WriteHeader(http.StatusOK) // HTTPステータスコードを200に設定する(省略すると200番)
json.NewEncoder(w).Encode(response) // JSON形式に変換して、レスポンスのボディに書き込む
```

### エラー処理
エラーも同様の流れで、ステータスコードを設定して、エラーメッセージをJSON形式で返す。
この時、エラーメッセージは専用の構造体を定義して、JSON形式に変換して返すと、フロントエンドで扱いやすい。
```go
w.Header().Set("Content-Type", "application/json") // JSON形式でレスポンスを返すことを明示する
w.WriteHeader(http.StatusInternalServerError) // HTTPステータスコードを500に設定する(省略すると200番)
json.NewEncoder(w).Encode(ErrorResponse{"データベースの読み込みに失敗しました"}) // JSON形式に変換して、レスポンスのボディに書き込む
```
```go
type ErrorResponse struct {
    Message string `json:"message"`
}
```

### `json.NewEncoder(w).Encode(response)`について
二つに分割でき、
```go
encoder := json.NewEncoder(w) // レスポンスのボディに書き込むためのエンコーダーを作成する
encoder.Encode(response) // レスポンスをJSON形式に変換して、レスポンスのボディに書き込む
```

## Tips
switch文のcaseは、breakが不要。
`gofmt -w [ファイル名]`でGo公式ガイドラインに沿ってコードを自動整形できる。

# Vite
proxy設定: `vite.config.ts`を編集することで、フロントのポートからバックエンドのポートにリクエストを転送できるようにする。（セキュリティの観点からブラウザがブロックしてしまうため）

# Typescript
## fetch
apiを叩いてデータを取得するために、`fetch`関数を使用する。
```typescript
const response = await fetch("/api/path");
if (!response.ok) {
    // エラー処理
}
const data = await response.json();
```
