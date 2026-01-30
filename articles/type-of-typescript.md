---
title: "TypeScriptの型に関して"
emoji: "🐷"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [TypeScript, 型定義, フロントエンド, バックエンド]
published: true
---
# 型定義のテクニック

## import type の活用
`import type { XXX } from 'YYY'` を使うことで、明示的に型のみをインポートできる。

- **メリット:** JSへトランスパイル後にimport文が消去されるため、バンドルサイズへの影響や循環参照のリスクを減らせる。
- **バックエンドの型利用:** フロントエンド側でNode.js由来の型（バックエンドのコード）を不用意に読み込むとランタイムエラーになることがあるが、`import type`であれば型情報のみを参照するため、安全に利用できる場合がある（ただし、アーキテクチャとしては後述のOpenAPI等の利用が推奨される）。

## as const (constアサーション)
オブジェクトや配列の末尾に `as const` を付けることで、リテラル型として扱える。

```ts
const fruits = ['apple', 'banana'] as const;
// type: readonly ["apple", "banana"]
// "apple" | "banana" 型として扱えるようになる

const num = 42 as const;
// type: 42

const list = [1, 2, 3];
list.push(4); // 問題なし

const list = [1, 2, 3] as const;
list.push(4); // エラー: readonly配列なので変更不可
```

- プロパティがすべて `readonly` になり、変更不可になる。
- 文字列型の定数をUnion型として扱いたい場合などに便利。

# フロントエンドとバックエンドの型共有

フロントとバックでどうやって型定義を同期するかについては、以下の3つの方法がある。

## 1. OpenAPI (Schema First)
API設計書（`openapi.yaml`）を先に定義し、そこから型定義ファイルを自動生成する方法。

- **メリット:** `api-types.ts` のようなファイルを自動生成でき、フロントとバックの型乖離を防げる。
- **Tips:** 生成時は `.d.ts` ではなく `.ts` として生成すると、`path` から返り値の型を直接参照できる。

```ts
// 例: パスの定義からレスポンスの型を取得する
import type { paths } from './api-types';
type UserResponse = paths['/users/{id}']['get']['responses']['200']['application/json'];
```

## 2. tRPC (TypeScript $\times$ Node.js)
バックエンドもTypeScript（Node.js）で実装している場合、tRPCが使用できる。
スキーマ定義ファイルを経由せず、コードベースで直接型を共有できるため、楽に安全な開発ができる。
デメリットは、外部にAPIを公開しようとする時には、結局OpenAPIなどのスキーマ定義が必要になること。

## 3. 共有ディレクトリ (Monorepo構成など)
シンプルな構成であれば、共通の型定義フォルダを作成するのも一つの手。
- `app/share/types`: 共通の型
- `app/front/`: フロントエンド
- `app/back/`: バックエンド

ただし、フロントエンドからバックエンド固有のライブラリ（Node.jsのモジュール等）に依存しないよう注意が必要。
