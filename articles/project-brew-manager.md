---
title: "brewManagerプロジェクトで学習したことのまとめ"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
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

