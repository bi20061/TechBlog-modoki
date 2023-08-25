---
title: "【整理】Reactの概要"
date: "2023-08-25"
---

## はじめに

React の基礎を学習してきた中で重要なだと感じた部分をまとめたいと思います．

---

## 1. JSX

JSX とは JavaScript の拡張言語．  
JSX = HTML ライクな記述＋ JavaScript の構文が使える．

### JSX は何をしているのか

- コンパイル時
  1. JSX を React.createElement の式に変換
  2. React 要素を生成
  3. React 要素から HTML を生成

### 特殊な JSX の構文

- 最上位コンポーネントとは並列にできないのでタグで囲む必要がある
- 要素が必要ないなら div タグ等で囲むのではなく React.Fragment で囲む
- React.Fragment は省略できる

```jsx
return (
  <React.Fragment>
    <p>新・日本一わかりやすいReact入門</p>
    <p>JSXの基礎文法を解説します</p>
  </React.Fragment>
);

// 省略記法
return (
  <>
    <p>新・日本一わかりやすいReact入門</p>
    <p>JSXの基礎文法を解説します</p>
  </>
);
```

---

## 2. コンポーネントと props

コンポーネント：見た目と機能を持つ UI 部品  
コンポーネントを組み合わせてページを作成する．

### なぜコンポーネントを使うのか

- 再利用するため
- コードの見通しを良くするため
- 変更に強くするため

### export の種類

- normal export
  - 名称が異なるとエラーになる
  - as を使用することで別名で import は可能

```jsx
// export側
export const SomeComponent = () => {};

// import側
import { SomeComponent } from "./SomeComponent";
// 名称が異なるのでエラーとなる
import { Some } from "./SomeComponent";
// Someという名前で使用できる
import {SomeComponent as Some} "./SomeComponent"
```

- default export
  - import 側は{}は必要ない
  - 任意の名前をつけて import が可能
  - 一つのファイルで一回しか使えない

```jsx
// export側
const someComponent = () => {};
export default SomeComponent;

// import側
import some from "./SomeComponent";
```

import 時に名前を間違えてしまっても気づかなかったり，意図しないものを import する可能性があるため normal export を基本的に使う方がいいらしい．

### props へ関数を渡す際の注意点

- OK な関数の渡し方
  ```jsx
  <PublishButton isPublished={isPublished} onClick={publishArticle} />
  <PublishButton isPublished={isPublished} onClick={() => publishArticle()} />
  ```
- NG な関数の渡し方(無限レンダリングが起きる)
  ```jsx
  <PublishButton isPublished={isPublished} onClick={publishArticle()} />
  ```

---

## 3. useState

### なぜ state を使うのか

- React コンポーネントが再描画するきっかけ
  - state が変更されたとき
  - props が変更されたとき
- React コンポーネント内の値を書き換えたい場合
  新しい値を使って再描画(再レンダリング)させる

### props と state の違い

- props は引数のようにコンポーネントに渡される値
- state はコンポーネントの内部で宣言・制御される値

### useState の使い方

- useState による state の宣言
  - 変数名は自由に宣言できる
  - state：現在の状態
  - setState：更新関数
  - initialState：初期値
  - newState：新しい値

```jsx
const [state, setState] = useState(initialState);
```

- state の更新

```jsx
setState(newState);
```

### prevState

prevState：更新前の state の値

```jsx
const Counter = () => {
  const [count, setCount] = useState(0);

  const countUp = () => {
    setCount((prevState) => prevState + 1);
  };

  const countDown = () => {
    setCount((prevState) => prevState - 1);
  };

  return (
    <div>
      <p>現在のカウント数：{count}</p>
      <button onClick={countUp}>up</button>
      <button onClick={countDown}>down</button>
    </div>
  );
};
```

useState の更新関数は少しラグがあるため setCount(count + 1)とするとクリックした回数が正常にカウントされない可能性がある.

preveState を使用すると前の state の値を必ず参照するので正常にカウントされる．

---

## 4. useEffect

useEffect：レンダリングされる度に処理したいことを呼び出せる Hooks.

```jsx
const Counter = () => {
  const [count, setCount] = useState(0);

  const countUp = () => {
    setCount((prevState) => prevState + 1);
  };

  const countDown = () => {
    setCount((prevState) => prevState - 1);
  };

  useEffect(() => {
    console.log("Current count is ...", count);
  });

  return (
    <div>
      <p>現在のカウント数：{count}</p>
      <button onClick={countUp}>up</button>
      <button onClick={countDown}>down</button>
    </div>
  );
};
```

コンソールログに count の値が変わるたびに表示される．

### 第二引数の依存関係

```jsx
// 毎回実行される
useEffect(() => {
  console.log("Current count is ...", count);
});

// 初回レンダリング後のみ実行される
useEffect(() => {
  console.log("Current count is ...", count);
}, []);

// countが変更される度に実行される
useEffect(() => {
  console.log("Current count is ...", count);
}, [count]);

// countかtrigerが変更される度に実行される
useEffect(() => {
  console.log("Current count is ...", count);
}, [count, triger]);
```

### クリーンアップ

データベースとの通信のとき，購読しっぱなしは良くないので購読を解除する必要がある．その時にクリーンアップを使用する．

useEffect 内の return で関数を返すことで実装ができる．

```jsx
const ToggleButton = () => {
  const [open, setOpen] = useState(false);

  const toggle = () => {
    setOpen((prevState) => !preveState);
  };

  useEffect(() => {
    console.log("Current state is", open);
    if (open) {
      console.log("Subscribe database...");
    }
    return () => {
      console.log("Unsubscribe database!");
    };
  });

  return <button onClick={toggle}>{open ? "OPEN" : "CLOSE"}</button>;
};
```

---

## 参考サイト・文献

- [新・日本一わかりやすい React 入門【基礎編】](https://www.youtube.com/playlist?list=PLX8Rsrpnn3IWPoM7-1YPDksRRkamRY25k)

- [React 実践の教科書](https://www.amazon.co.jp/%E3%83%A2%E3%83%80%E3%83%B3JavaScript%E3%81%AE%E5%9F%BA%E6%9C%AC%E3%81%8B%E3%82%89%E5%A7%8B%E3%82%81%E3%82%8B-React%E5%AE%9F%E8%B7%B5%E3%81%AE%E6%95%99%E7%A7%91%E6%9B%B8-%EF%BC%88%E6%9C%80%E6%96%B0ReactHooks%E5%AF%BE%E5%BF%9C%EF%BC%89-%E3%81%98%E3%82%83%E3%81%91%E3%81%87%EF%BC%88%E5%B2%A1%E7%94%B0-%E6%8B%93%E5%B7%B3%EF%BC%89-ebook/dp/B09BV2HGN3/ref=sr_1_5?crid=O2LFF9U3Q2JW&keywords=react+%E6%9C%AC&qid=1692944734&sprefix=react+%2Caps%2C212&sr=8-5)
