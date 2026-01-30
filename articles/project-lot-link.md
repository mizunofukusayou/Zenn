---
title: "Lot-Linkプロジェクトで学習したことのまとめ"
emoji: "🙌"
type: "tech"
topics: ["Project", "Typescript", "React", "CSS"]
published: true
---

# はじめに

[Lot-Link](https://github.com/mizunofukusayou/Lot-Link)というWebアプリ開発プロジェクトを通じて学んだ、細かいけれど重要な技術的な知見をまとめる。
長くなりそうなトピックは別記事に切り出しているが、ここではそれ以外の細かなTipsや設計周りの気づきを備忘録として残す。

# TypeScript周りの知見

https://zenn.dev/mizunofukusayou/articles/type-of-typescript

---

# React

### keyプロパティの設置場所
リストレンダリング時の `key` は、できるだけ**親のコンポーネント（リストのルート要素）**で設定すべき。
不適切な場所に設定すると、Reactが要素の移動や変更を正しく検知できず、不要な再レンダリングや予期せぬ挙動（入力フォームの状態が残るなど）の原因になる。

---

# CSS

https://zenn.dev/mizunofukusayou/articles/how-to-use-grid

https://zenn.dev/mizunofukusayou/articles/truncating-url

---

# その他

### JSONで安全に扱えるデータ型
JSON形式でデータをやり取り・保存する際、以下の型以外（Date型やFunction、undefinedなど）は自動的に削除されたり変換されたりするため注意が必要。

- string
- number
- boolean
- null
- array
- object