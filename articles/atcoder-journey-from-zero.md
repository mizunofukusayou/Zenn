---
title: "AtCoderの成長記録"
emoji: "🐡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [AtCoder]
published: false
---

# ABC
## 455
- 多重ループを回すときに、`rep(i, n) rep(j, m)`のように書くと、インデントが浅くなって、コードが見やすくなる。

## 453
- stringに対して、`substr(pos, len)`とすることで、posからlen文字分の部分文字列を取得できる。

## 450
- CTADにより、vectorの中身を推論してくれるので、`vector v(n, vector<int>(m));`のように書くことができる。

## 446
- `'a'-'A'=32`であるから、`char c = 'A'; c^=32;`(XOR)とすることで、cを小文字に変換できる。
- `char c = 'a'; c &= ~32;`とすることで、cを大文字に変換できる。
- `auto exist = std::views::iota(1, m + 1) | std::ranges::to<std::set<int>>();`とすることで、1からmまでの整数を要素とするsetを作ることができる。
