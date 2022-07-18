<!--
title:   【Javascript】Javascriptのfetchの仕様の注意点
tags:    JavaScript,TypeScript,error,fetch
id:      7f67547a571749ea235b
private: false
-->


# 初めに

React での開発中に、`fetch`の仕様がわからなくなったのでここに忘備録を残しておきます。

## 環境

- React 18.0.0
- typescript 4.6.3

# Fetch API の仕様

## json データの取り出し方

> `Fetch API`の最も簡単な使い方は 1 つの引数 — fetch で取得したいリソースへのパス — のみをとり、 Response オブジェクトで解決するプロミスを返します。Response は、実際の JSON レスポンス本体を直接持っているのではなく、 HTTP レスポンス全体を表現するものです。 Response オブジェクトから JSON の本文の内容を抽出するには、 json() メソッドを使用します。これはレスポンス本文のテキストを JSON として解釈した結果で解決する第 2 のプロミスを返します。

上記のように、`fetch()`は取得した`Response`に対して`json()`メソッドを使用しないと、レスポンスの本体が取得できません。

```javascript
fetch(URL)
  .then((response) => response.json())
  .then((json) => console.log(json));
// jsonにはレスポンスの本体が入っている
```

## レスポンスが成功したかの確認方法

> 404 はネットワークエラーにはなりません。 fetch() が成功したかどうかを正確に判定するには、プロミスが解決された後で、 Response.ok プロパティが true になっているかを確認してください

上記に記載されているように、`Reasponse.ok`での確認が必ず必要になります。

```javascript
fetch(URL)
  .then((response) => {
    if (!response.ok) {
      throw new Error("Network response was not ok.");
      // Errorが発生したら、ここで処理を中断して、下記のcatchに移行する
    }
    return response.json();
  })
  .then((json) => console.log(json))
  .catch((error) => console.log(error));
```

## Typescript 使用時の注意点

レスポンスの`json`が `any`型もしくは `unknown`型になる

```javascript
fetch(URL)
  .then((response) => {
    if (!response.ok) {
      throw new Error("Network response was not ok.");
    }
    return response.json();
  })
  .then((json) => console.log(json))
  // jsonの型がany型になる
  .catch((error) => console.log(error));
```

これを解決するには`fetch`をラップして、レスポンスに型を指定する必要があります。