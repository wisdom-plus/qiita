<!--
title:   【Stimulus】Stimulusをクリックした要素を取得する方法
tags:    JavaScript,Rails7,Stimulus
id:      b29f272230fd7ec199ea
private: false
-->


# はじめに

Rails7 でデフォルトでインストールされるようになった Stimulus でクリックした要素を取得する方法を忘備録として残しておきます。

## 環境

Rails 7.0.2
Ruby 3.1.1
Stimulus-rails 1.0.4

# 取得方法

ボタンが４つあり特定のボタンをクリックした時だけ、アクションを起こしたい時があると思います。
通常なら`data-target`を利用することで、javascript 側で要素を取得することができます。
ですが、それが使えないときは`Stimulus`のメソッドの引数を利用することで取得することができます。

## html

```html: button.html
<div data-controller='button'>
  <button id='1' data-action='click->button#next'>
    1
  </button>
  <button id='2' data-action='click->button#next'>
    2
  </button>
  <button id='3' data-action='click->button#next'>
    3
  </button>
  <button id='4' data-action='click->button#next'>
    4
  </button>
</div>

```

## javascript

メソッドの引数の`currentTarget`にクリックした要素が入っています

```javascript:button_controller.js
import { Controller} from '@hotwired/stimulus';

export default class extends Controller {

  next(event) {
    const button_id = event.currentTarget.id;
    if (button_id == 1) {
      console.log(button_id)
    }
  }
}

```

# 参考

やっぱり困った時は、公式サイトのリファレンスを見るのが一番です

[公式サイト](https://stimulus.hotwired.dev/reference/actions)