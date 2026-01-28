---
title: "URLを省略した形で表示する方法"
emoji: "📝"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [URL, CSS]
published: true
---
# URLをCSSを用いて省略して表示する方法
- `overflow-wrap: break-word;`で幅に幅に合わせて(単語を切らずに)改行してくれる。
(`word-break: break-all;`だと単語の途中で改行される)

- `display: -webkit-box;`と`-webkit-line-clamp: n`で`...`をつけた状態で$n$行目までを表示しする。

- `-webkit-box-orient: vertical;`は`-webkit-line-clamp`が今何行積み重ねられているかを認識するために必要。

- `overflow: hidden;`ではみ出した部分を隠す。

```css
.shortened-url {
  overflow-wrap: break-word;
  display: -webkit-box;
  -webkit-line-clamp: 2; /* 表示したい行数 */
  -webkit-box-orient: vertical;
  overflow: hidden;
}
```

# Tailwindの場合
```ts
<div className="wrap-break-word line-clamp-2"></div>
```