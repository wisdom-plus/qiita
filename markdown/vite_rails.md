<!--
title: 【Rails】Javascriptバンドラーの第5の選択肢vite_rails
tags: Rails,Javascript,Vite
-->

# 初めに

`Rails7`からアセットパイプラインは`importmap` `esbuild` `webpack` `rollup`の四つの選択肢があります。
ですが、新たなに`vite_rails`という選択肢をここで紹介させていただきます。

# なぜ vite_rails なのか

- 開発サーバーは非常に高速に起動し、変更が即座に更新される。
- 基本的にデフォルトの設定で動作し、構成が簡単である
- デプロイ時は`assets:precompile`でバンドルができる。
- css や sass などもバンドルできる。

# 導入方法

`webpacker`からの移行する方法になります。
`Gemfile`に`vite_rails`を追加します。

```Gemfile:Gemfile
gem 'vite_rails'
```

```console:ターミナル
$ bundle install

$ bundle exec vite install
設定ファイルを作成する
```

デフォルトでは`app/frontend`がエントリーポイントになっていますので、`app/javascript`に変更します。

```json:config/vite.json

{
  "all": {
    "sourceCodeDir": "app/javascript",
  }
  // 省略
}

```

`application.html.erb`にある読み込みのタグヘルパーを変更する。

```erb:app/views/layouts/application.html.erb

- <%= javascript_packs_with_chunks_tag 'application' %>
+ <%= vite_javascript_tag 'application' %>

```

そして、`require.context`を`import.meta.globEager`に書き換える。

```javascript
- const context = require.context("./controllers", true, /\.js$/)
+ const controllers = import.meta.globEager('./**/*_controller.js')
```

# 参考

- [Vite Ruby](https://vite-ruby.netlify.app/)
- [migration to vite - Vite Ruby](https://vite-ruby.netlify.app/guide/migration.html#webpacker-%F0%9F%93%A6)
