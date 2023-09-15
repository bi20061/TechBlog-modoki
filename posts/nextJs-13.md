---
title: "【Next.js13入門】Next.js 13でTodoアプリ作成"
date: "2023-09-01"
---

## はじめに

Next.js で Todo アプリを作成する中で Next.js 13 になって何が変わったのか勉強しようと思います．

---

## 1. 変更箇所

### src/app

- 前バージョンの Next.js の page ファイルのような感じらしい．
- ページのルーティングを担っている．
- ファイルは全てサーバーサイドでレンダリングされる．

個人的には，環境構築する際に自動で **TailwindCSS** がインストールが出来るようになったのが嬉しい！

---

## 2. json-server

ローカルで json 形式を保存できるライブラリ．モックデータとかテストで使用する時にいいらしい．

1. src ディレクトリの下に data ディレクトリを作成

2. package.json に json-server を起動するコマンドを記述

```json
// package.jsonのscriptに記述
{
  "scripts": {
    "json-server": "json-server --watch src/data/todos.json --port 3001"
  }
}
```

3. todo.json ファイルを作成

```json
// todos.jsonファイル
    {
  "tasks": [
    {
      "id": "1"
      "text": "散歩",
    },
    {
      "id": "2",
      "text": "プログラミング"
    }
  ]
}

```

4. npm run json-server でサーバーが起動できる．

---

## 3. API を作成

### json サーバーに格納されているデータを取得するための API を作成

```ts
export const getAllTodos = async (): Promise<Task[]> => {
  const res = await fetch(`${baseUrl}/tasks`, { cache: "no-store" }); //cache: "no-store"でSSRを指定している．
  const todos = await res.json();
  return todos;
};
```

### タスクを追加するための API を作成

```ts
export const addTodo = async (todo: Task): Promise<Task> => {
  const res = await fetch(`${baseUrl}/tasks`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify(todo),
  });
  const newTodo = await res.json();
  return newTodo;
};
```

addTodo を呼び出す関数．id を自動で生成するために uuid というライブラリをインストールした．

```ts
import { v4 as uuidv4 } from "uuid";

const handleSubmit = async (e: FormEvent) => {
  e.preventDefault();
  await addTodo({ id: uuidv4(), text: newTaskValue }); // uuidv4()で自動でランダムなidが生成される
  setNewTaskValue("");

  router.refresh();
};
```

### タスクを修正するための API を作成

```ts
export const updateTodo = async (
  id: string,
  newText: string
): Promise<Task> => {
  const res = await fetch(`${baseUrl}/tasks/${id}`, {
    method: "PUT",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({ text: newText }),
  });
  const updatedTodo = await res.json();
  return updatedTodo;
};
```

## タスクを削除するための API を作成

```ts
export const deleteTodo = async (id: string): Promise<Task> => {
  const res = await fetch(`${baseUrl}/tasks/${id}`, {
    method: "DELETE",
    headers: {
      "Content-Type": "application/json",
    },
  });
  const deleteTodo = await res.json();
  return deleteTodo;
};
```

---

## 番外編

### heroicons

動画では解説されていませんが，shin さんの github のコードを見ると [heroicons](https://heroicons.com/) というアイコンを表示するためのツールを用いてます．  
そのため実装しようとしたのですが，私はここでつまづきました．単純に heroicons を import すると…

**モジュール '{heroicons/react}' の宣言ファイルが見つかりませんでした。'{heroicons/react}' は暗黙的に 'any' 型になります。
存在する場合は `npm i --save-dev @types/{heroicons/react}` を試すか、`declare module '{heroicons/react}';` を含む新しい宣言 (.d.ts) ファイルを追加します**

というエラーがでます．  
はじめは npm i --save-dev @type/{@heroicons/react}を実行したのですが，そのようなライブラリはないと怒られます…  
どうやら heroicons は TypeScript 用の型定義ファイルが用意されていないため，`declare module '{モジュール名}';` を含む新しい宣言 (.d.ts) ファイルを追加することででしか解決できないようです．

```ts
declare module "@heroicons/react/solid";
```

このファイルを作成するとエラーが無くなりました！

---

## まとめ

api.ts ファイルに API を記述し，AddTask.tsx，Todo.tsx の関数の中で呼び出すことで，ボタンがクリックされた時に api にテキスト情報や id を送ることができるようにすることで ToDo アプリを開発しました．  
SSR と CSR の違いそれぞれのメリット・デメリットがまだあやふやなので，勉強が必要だなと感じました．

---

## 参考サイト

- [Todo アプリを作りながら Next.js13 の新機能を理解してみよう ～ Next.js13 入門～](https://www.youtube.com/watch?v=VcMW2C9VNtI&t=1303s&ab_channel=%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0%E3%83%81%E3%83%A5%E3%83%BC%E3%83%88%E3%83%AA%E3%82%A2%E3%83%AB)
- [heroicons](https://heroicons.com/)
- [型定義ファイルのないライブラリのインポートエラーについて](https://zenn.dev/ymmt1089/articles/20220429_interface)
