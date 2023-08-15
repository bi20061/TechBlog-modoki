---
title: '【TypeScript入門】TypeScriptの概要'
date: '2023-08-12'
---

## なぜTypeScriptが使われているのか
Javascriptに型システムが組み合わさったのものがTypeScript．
型システムがあることで，コードの安全性が向上しバグが発見しやすくなる．


## 1. 基本の型定義とアノテーション
### プリミティブ型

```ts 
// string: 全ての文字列を扱う型
const name: string = 'hogetarou'

// number: 全ての数値を扱う方
const age: number = 25

// boolean: trueとfallseの2つの値を扱う型
const flag: boolean = true
```

### 存在しないことを表す型
```ts 
// null: 値が欠如していることを表す
const name = null

// undefined: 初期化されておらず値が割り当てられてないことを表す
const age = undefined

```

### any型とunknown型
```ts 
// any: どんな型でも許容する=全く安全ではない
const name: any = null

// unknown: どんな型になるのか不明
const maybeNumber: unknown = 10
const isFoo = maybeNuber === 'foo' //：これはできる
const sum = maybeNuber + 10 //：unknown型だから足し算できないと怒られる

// 型を判定すれば使用することができる
if (typeof maybeNumber === 'number') {
    const sum = maybeNumber + 10
}

```
anyを使ったらTypeScriptを使う意味がない．unknownは後から型定義をしたい場合に使用される．
