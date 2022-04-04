<!--
title:   【Stimulus】targetがある時だけ関数を実行する方法
tags:    JavaScript,Rails,Stimulus,Turbo
id:      33e3c6d01f529b8d1003
private: false
-->

# はじめに

Rails7 で Javascript を利用するときは Stimulus を使うのがデフォルトになっています。

特定のページで関数を実行する方法を書き残しておきます。

## 環境

- Rails 7.0.22
- Ruby 3.1.1
- Rspec 3.10.0
- stimulus-rails 1.0.4

## やりたい事

特定のページの時だけクラスを削除する関数を実行する

# 本文

Stimulus のライフサイクルコールバックと呼ばれる特別なメソッドを使用すると目的を達成することができます。

`TargetConnected()`を利用する

```javascript:class_controller.js
import { Controller } from "@hotwired/stimulus";

// Connects to data-controller="class"
export default class extends Controller {
  static target = ["class"];

  classTargetConnected() { //ターゲット名が入る
    const target = this.classTarget // これでdata-target='class'がついている要素が取得できる
    target.classList.remove("remove-target");
  }
}

```

```html: target.html.erb
<div data-controller='class'> <!--data-controllerがない起動しない -->
  <p>target</p>
  <p class= 'remvoe-target' data-class-target='class'>remove-target</p>
  <!--data-class-targetがDOMに接続されるとclassTargetConnectedが実行される -->
</div>
```

# 参考

[公式リファレンスーライフサイクルコールバック](https://stimulus.hotwired.dev/reference/lifecycle-callbacks)

[Github stimulus](https://github.com/hotwired/stimulus)
