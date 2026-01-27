---
title: "Grid(CSS)について"
emoji: "🧱"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Grid", "CSS"]
published: true
---
# Gridとは
cssのレイアウトを組むための手法の一つで、Flexboxが一次元的なレイアウトを組むときに使用するのに対して、Gridは二次元的なレイアウトを組むときに便利。

# 使い方
Gridを使用するには、親要素に対して`display: grid;`を指定して、`grid-template-columns`や`grid-template-rows`で列や行のサイズを指定する。
> **[Grid Garden](https://cssgridgarden.com/#ja)**
> これがわかりやすくて最高。

# `auto-fill`と`auto-fit`
`grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));`のように使用することで、コンテナの幅に応じて自動的に列数を調整できる。
この場合、
$\text{grid-template-columns: repeat(}\lfloor\, \frac{100\%}{200px}\rfloor \text{, 1fr)}$
と同じ意味になる。
`auto-fit`の場合は、
$\text{grid-template-columns: repeat(min(}\lfloor\, \frac{100\%}{200px}\rfloor \text{, 要素数), 1fr)}$