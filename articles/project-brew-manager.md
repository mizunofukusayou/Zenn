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

### Note
main関数でのエラー出力に関して、
User Error
- `fmt.Fprintf(os.Stderr, "エラーメッセージ")`で、標準エラー出力にエラーメッセージを出力する。
これを使用すると、標準エラー出力に、エラーメッセージだけを出力する。
`log.Printf`を使用すると、標準エラー出力に、エラーメッセージと、日付と時間が出力される。
`fmt.Printf`は、標準出力に出力するため、CLIだとパイプで繋げた時に困る。

System Error
- `log.Printf`は小規模なプロジェクト用
- `log/slog`は大規模なプロジェクト用（ログのレベル分けや、ファイル出力などができる）
- `log.Fatalf`は、エラーを出力した後に、プログラムを強制終了させる。(deferも実行されない)(`log.Printf + os.Exit(1)`と同等)


## Tips
switch文のcaseは、breakが不要。
`gofmt -w [ファイル名]`でGo公式ガイドラインに沿ってコードを自動整形できる。