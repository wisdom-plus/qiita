<!--
title: 【Rails】Rails６でのjQueryを使うときのwebpackerの設定
tags: Rails,javascript,webpacker,jquery
-->

# 初めに

`Rails6`での`webpacker` の設定が分からなくなっていたのでここで書き残しておきます。

## 環境

- Rails 6.1.4
- Ruby 2.7.4
- webpacker 5.4.3

# webpacker

Rails6 の javascript の設定は config/webpacke の下にあるファイルで行います。
今回は jquery が使えるようにします。

## enviroment.js

```javascript:config/webpack/environment.js
const { environment } = require("@rails/webpacker");

const webpack = require("webpack");

environment.plugins.prepend(
  "Provide",
  new webpack.ProvidePlugin({
    $: "jquery",
    jQuery: "jquery",
    jquery: "jquery",
    Popper: ["popper.js", "default"],
  })
);
module.exports = environment;

```

## application.js

```javascript:javasccript/packs/application.js

require("@rails/ujs").start();
require("turbolinks").start();
require("@rails/activestorage").start();
require("channels");

import $ from "jquery/dist/jquery.js

globalThis.jQuery = $;
globalThis.$ = $;

```

これで jquery がエラーなく動きます。

# 参考

[Issue loading jQuery in Webpacker 5- stackoverflow](https://stackoverflow.com/questions/69831828/issue-loading-jquery-in-webpacker-5)
