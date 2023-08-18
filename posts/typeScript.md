---
title: '【TypeScript入門】TypeScriptの概要'
date: '2023-08-12'
---

## なぜTypeScriptが使われているのか
Javascriptに型システムが組み合わさったのものがTypeScript．
型システムがあることで，コードの安全性が向上しバグが発見しやすくなる．

---
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

### nullとundefined
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

---
## 2. 関数

### 用語の整理
1. パラメータ：関数宣言時に渡される値
2. 引数：関数を呼び出す時に渡す値
3. 戻り値；関数が返す値

### voidとnever
- void：return文を持たない関数の戻り値
```ts
const logMessage = (message: string): void => {
    console.log('Function basic sample:', message)
}

```

- never：決して戻ることのない関数の戻り値
```ts
const alwaysThrowError = (message: string): never => {
    throw new Error(message)
}

```

### オプションとデフォルト
#### オプションパラメータ
- 渡す引数を省略することが可能になる
- パラメータの最後に記述し，？をつける
```ts
const isUserSignedIn = (userId: string, username?: string): boolean => {
    // 処理
}
```

#### デフォルトパラメータ
- 渡す引数が省略された場合に代入される値を指定
- = で指定
```ts
const isUserSignedIn2 = (
    userId: string,
    username = 'NO NAME'
): boolean => {
    // 処理
}
```

### レストパラメータ
- 関数に任意の数の引数を渡すことが可能になる
- パラメータの最後に一つだけ，...を用いることで使用できる

```ts
const sumPrice = (...price: number[]): number => {
    // 処理
}
```

### 呼び出しシグネチャ
- どのような関数なのかを表現する型定義
- 省略記法はアロー関数と似た形
- 完全な記法はオブジェクトと似た形

```ts
// 省略記法
type LogMessage = (message: string) => void

// 完全な記法
type FullLogMessage = {
    (message: string): void
}

const logMessage: LogMessage = (message) => {
    // 処理
}
```
---
## 3. 型エイリアス(type)でオブジェクトの型定義

### オブジェクトリテラル記法
```ts
let country: {
    language: string
    name: string
} = {
    language: "Japanaese",
    name: "Japan"
}

// 再代入しても型が担保された状態で定義することができる
country = {
    language: "English",
    name: "United States of America"
}
```

### オプショナルとreadonly

- オプショナル

    オプショナルのついたプロパティはあってもなくてもOK
- readonly

    readonlyのついたプロパティは上書きができない

```ts
const human: {
    age: number
    lastName: string
    readonly firstName: string
    gender?: string
} = {
    age: 28,
    lastName: "Ymada",
    firstName: "Tarou"
}

human.lastName = "Kamado"
// オプショナルのためここで代入することも可能
human.gender = "male"
// readonlyなので上書きできない
human.firstName = "Tanjiro"
```

### インデックスシグネチャ
- オブジェクトが複数のプロパティを持つ可能性を示す
- keyはstringかnumberのみ
```ts
const capitals: {
    [contryName: string]: string
} = {
    Japan: 'Tokyo',
    Korea: 'Seoul'
}

capitals.China = 'Beijing'
capitals.Canada = 'Ottawa'
```

### 型エイリアスで型定義を再利用

#### 型エイリアスとは
- typeを使って，型に名前をつけて宣言できる
- 同じ型を何度も定義する必要がない
- 方に名前をつけることで変数の役割を明確化

```ts
type Country = {
    capital: string
    language: string
    name: string
}

const japan: Country = {
    capital: 'Tokyo',
    language: 'Japanese',
    name: 'Japan'
}

// Countryを再利用
const america: Country = {
    capital: 'Washington, D.C.',
    language: 'English',
    name: 'United State of America'
}
```

### 合併型と交差型
- 合併型：型Aか型Bどちらかの型を持つ
- 交差型：型Aと型B両方の型を持つ
```ts
type Knight = {
    hp: number
    sp: number
    weapon: string
    swordSkill: string
}

type Wizard = {
    hp: number
    mp: number
    weapon: stirng
    magicSkill: stirng
}

// 合併型
type Adventurer = Knight | Wizard

// 交差型
type Paladin = Knight & Wizard

// Knightよりの冒険者
const adventurer1: Adventurer = {
    hp: 100,
    sp: 30,
    weapon: '木の剣',
    swordSkill: '三連斬り'
}

// Wizard寄りの冒険者
const adventurer2: Adventurer = {
    hp: 100,
    mp: 30,
    weapon: '木の剣',
    magicSkill: 'ファイアボール'
}

// KnightとWizardの両方の型を持たないとエラーになる
const paladin: Paladin = {
    hp: 300,
    sp: 100,
    mp: 100,
    weapon: '銀の剣',
    swordSkill: '三連斬り',
    magicSkill: 'ファイアボール'
}
```
---
## 4. 配列とタプルの型定義

### 配列の型定義
```ts
// 2種類の定義方法がある
const odd: number[] = [1, 3, 5]
const even: Array<number> = [2, 4, 6]
```
### 配列の型推論

```ts
const generateSomeArray = () => {
    const _someArray = [] //any
    _someArray.push(123) // number[]
    _someArray.push('ABC') // (string | number)[]
    return _someArray
}

const someArray = generateSomeArray()
someArray.push(true) // (string | number)[]なのでエラーとなる
```

### タプル
- タプルは配列の各要素の数と型を定義できる
```ts
let response: [number, string] = [200, "OK"]

// 3つ目の要素があるのでエラー
response = [400, "Bad Request", "Email parameter is missing"]
// 一つ目がstringなのでエラー
response = ["400", "Bad Request"]
```
- レストパラメーターも使える
```ts
const animal: [string, ...string[]] = ["Dog", "Cat", "Rabit"]
animal.push("Bear")
```

### イミュータブルな配列
- JavaScriptの配列，タプルはconstで宣言してもミュータブル(書き換え可)
- readonlyでイミュータブルな配列/タプルが作れる

```ts
// ミュータブルな配列
const mutableNumbers: number[] = [1,2,3]
mutableNumber[2] = 4

// イミュータブルな配列
const commands: readonly string[] = ["get add", "git commit", "git push"]
commands.push("git fetch") // 追加不可
commands[2] = "git pull" // 代入不可

// いくつか定義方法がある
const numbers: ReadonlyArray<number> = [1, 2, 3]
const names: Readonly<string[]> = ["Tarou", "Kazu"]
```
---
## 5. ジェネリック型とポリモーフィズム

### 型を抽象化するジェネリック型

- 型の種類は異なるが同じデータの構造…共通化できそう🤔

```ts
const stringReduce = (array: string[], initialValue: string):string => {}
const numberReduce = (array: number[], initialValue: number): number => {}

```

- ジェネリック型パラメータ
  - 型をパラメータ化(後から実パラメータを渡す)
  - T, U, V, W などがよく使われる

```ts
type Reduce<T> = {
    (array: T[], initialValue: T): T
}

const reduce: Reduce<string> = (array, initialValue) => {}
```
### ジェネリックの宣言方法

```ts
// 完全な呼び出しシグネチャ(シグネチャ全体にジェネリック型を割り当てる)
type GenericReduce<T> = {
    (array: T[], initialValue: T): T
}

// 完全な呼び出しシグネチャ (個々のシグネチャにジェネリック型を割り当てる)
type GenericReduce2 = {
    <T>(array: T[], initialValue: T): T
    <U>(array: U[], initailValue: U): U
}

// 省略記法
type GenericReduce3<T> = (array: T[], initialValue: T) => T
type GenericReduce4 = <T>(array: T[], initialVaule: T) => T

// ２つパラメータを使う場合
type Map<T, U> = (array: T[], fn: (item: T) => U) => U[]

// string配列を受け取ってnumberに変換してreturn
const mapStringToNumbers: Map<string, number> = (array, fn) => {
    const result = []
    for (let i=0; i < array.length; i++ ) {
        const item = array[i]
        result[i] = fn(item)
    }
    return result
}

const numbers = mapStringsToNumbers(["123", "456", "789"], (item) => Number(item))
```

### ポリモーフィズム
- 多様性・多相性 = いろいろな形に変化できること
- ジェネリック型を用いると…
  - 型を抽象化して共通化できる
  - 呼び出すときに具体的な型を渡す
- 共通化のメリット
  - 共通化することでテスト範囲が限定的になる．
  - 再利用が可能になるので効率化につながる．

---
## 6. Typescriptでのオブジェクト指向開発

### クラスの3つの役割
1. まとめる：ある機能についてのデータと振る舞いをまとめる
2. 隠す：外部から参照・改変できないようにする
3. たくさん作る：同じ機能を持つクローン(インスタンス)を量産できる

### 用語の整理
- プロパティ

    クラスが持つデータ．フィールド，メンバ変数とも呼ばれる．

- メソッド
    
    クラスで宣言する関数のこと

- コンストラクタ

    クラスからインスタンスを作る時に行う初期化

- インスタンス

    クラスから作られたオブジェクト

    クラスの機能を持つクローンみたいなやつ

### 修飾子
- private：そのクラスでのみアクセス可能
- protected：そのクラスとサブクラスでのみアクセス可能
- public：どこからでもアクセス可能(デフォルト)

### 抽象クラス
- abstract修飾子のついたクラス
- 抽象クラスはインスタンス化できない
- 継承でサブクラスを作るため

---
## 7. InterfaceとType Aliasの違い
### Interfaceの宣言
- ype Aliasと違って = が不要
- 同名のinterfaceを宣言すると型が追加される

```ts
interface Bread {
    calories: number
}

interface Bread {
    type: string
}

//型が追加されるているのでtypeも定義する必要がある
const francePan: Bread = {
    calories: 350,
    type: "hard"
}


// 型エイリアスで表現
type MaboDofu = {
    calories: number
    spicyLevel: number
}

type Rice = {
    calories: number
    gram: number
}

type MaboDon = MaboDofu & Rice

const maboDon: MaboDon = {
    calories: 500,
    spicyLevel: 10,
    gram: 350
}
```

### Interfaceの継承
- extendsを使うことで継承したサブインターフェイスを作れる
- Type Aliasをextendsすることもできる

```ts
interface Book {
    page: number
    title: string
}

interface Magazine extends Book {
    cycle: 'daily' | 'weekly' | 'monthly' | 'yearly'
}

const jump: Magazine = {
    cycle: 'weekly',
    page: 300,
    title: '週刊少年ジャンプ'
}

// Type Aliasを継承
type BookType = {
    page: number
    title: string
}

interface Handbook extends BookType {
    theme: string
}

const cotrip: Handbook = {
    page: 120,
    title: 'ことりっぷ',
    theme: '旅行'
}
```

### Interfaceでclassに型を定義
- implementsを使ってclassに型を定義できる
```ts
interface Book {
    page: number
    title: string
}

class Comic implements Book {
    page: number;
    title: string;

    constructor(page: number, title: string) {
        this.page = page
        this.title = title
    }
}

const popularComic = new Comic(200, '新テニスの王子様')
```

### Type AliasとInterfaceの違いまとめ

||Type Alias| Interface|
| ---- | ---- | ----|
|用途|複数の場所で再利用する型に名前をつけるため|オブジェクト・クラス・関数の構造を定義するため|
|拡張性|同名のtypeを宣言するとエラー|同名のinterfaceを宣言するとマージされる|
|継承|継承はできず，交差型で新しいエイリアスを作る|extendsによる継承ができる|
|使用できる型|オブジェクトや関数以外のプリミティブ， 配列，タプル|オブジェクトと関数の型のみ|
|考慮事項|拡張しにくい不便さがある|拡張できることによりバグを生む可能性|
|いつ使う|アプリ開発|ライブラリ開発|

---
## 8. TypeScriptによる非同期処理
- 非同期処理
  - 複数の処理を並行して効率よく実行できる
  - 制御が難しい

- 対処法
  - Promiseやasync/awaitで非同期処理を同期的に制御する
  - 型をつけることでより分かりやすく！

### Promise型
- 非同期処理の実行結果はPromise<string>のように定義できる
  ```ts
  type FetchProfile = () => Promise<Profile | null>
  const fetchProfile: FetchProfile = () => {
    // 非同期処理を行い，最終的にProfileかnullを返す
  }
  ```

---

## 参考サイト
[日本一わかりやすいTypeScript入門【基礎編】](https://www.youtube.com/watch?v=kd8VH10jXwc&list=PLX8Rsrpnn3IW0REXnTWQp79mxCvHkIrad&ab_channel=%E3%80%90%E3%81%A8%E3%82%89%E3%82%BC%E3%83%9F%E3%80%91%E3%83%88%E3%83%A9%E3%83%8F%E3%83%83%E3%82%AF%E3%81%AE%E3%82%A8%E3%83%B3%E3%82%B8%E3%83%8B%E3%82%A2%E5%AD%A6%E7%BF%92%E8%AC%9B%E5%BA%A7)