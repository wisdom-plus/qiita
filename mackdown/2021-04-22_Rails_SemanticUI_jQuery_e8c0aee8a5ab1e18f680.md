<!--
title:   semantic uiのform validationの設定
tags:    Rails,SemanticUI,jQuery
id:      e8c0aee8a5ab1e18f680
private: false
-->

# はじめに

Web サイトのフォームによくある、値がないときにフォームが赤く変わり入力を促す機能を semantic ui を用いて実装したので、忘備録として書き残しておきます。

# 環境

- Rails 6.1.3
- semantic_ui 2.4

# 使用方法

```ruby:application.js
$('.ui.form').
  form({
    fields: {
      name: 'empty',
      password: ['empty','minLength[6]','match[password_confirmation]'],
      password: 'empty'
      })
```

input タグの id,name,data-validate のどれかの要素と fields の識別子と一致しないと動かないです。

# Validation Rules

主なバリデーションのルールを書いておきます

|     name     |                     description                     |
| :----------: | :-------------------------------------------------: |
|    empty     |             フィールドの値が空であるが              |
|    email     |               有効なメールアドレスか                |
|     url      |                 入力した値が URL か                 |
| minLength[n] |          入力した値が n 以上の長さであるが          |
|   match[n]   |      引数で指定したフィールドと値が一致するか       |
| minCount[n]  | 複数選択フィールドで少なくとも n 以上の選択があるか |

# エラーメッセージ

デフォルトでは、エラーメッセージが英語のため書き換える必要があり、prompt を書き換えてください

```javascript
$('.ui.form').
  form({
    fields: {
      name: 'empty',
      password: ['empty','minLength[6]','match[password_confirmation]'],
      password: 'empty'
      },
    prompt: {
     empty: '{name}が入力さていません。',
     match: '{name}が{ruleValue}は一致しません。'
   }
  )
```

ちなみに、エラーメッセージが表示されない場合は form 内にエラーメッセージの HTML を設置する必要があります

```html:view
<div class="ui error message"></div>
```

# 参考

[semantic ui form validation](https://semantic-ui.com/behaviors/form.html#/usage)
