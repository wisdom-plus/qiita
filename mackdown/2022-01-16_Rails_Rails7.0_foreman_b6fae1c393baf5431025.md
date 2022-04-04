<!--
title:   【Rails】Rails7.0でyarn build yarn build:cssが起動しない
tags:    Rails,Rails7.0,foreman
id:      b6fae1c393baf5431025
private: false
-->

# 前提

Rails 7.0.1
Ruby 2.7.2
docker 20.10.11

### 発生したエラー

rails new -j esbuild で作成した後にサーバーを起動させる際に yarn build と yarn build:css がエラーが出てバンドルされない状態になってしまう。

```console:console
~/rails(master) $ bin/dev
20:59:27 web.1  | started with pid 94024
20:59:27 js.1   | started with pid 94025
20:59:27 css.1  | started with pid 94026
20:59:27 js.1   | yarn run v1.22.15
20:59:27 css.1  | yarn run v1.22.15
20:59:27 js.1   | error Command "build" not found.
20:59:27 js.1   | info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
20:59:27 css.1  | error Command "build:css" not found.
20:59:27 css.1  | info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
20:59:28 js.1   | exited with code 1
20:59:28 system | sending SIGTERM to all processes
20:59:28 css.1  | exited with code 1
20:59:28 web.1  | terminated by SIGTERM
```

# 本文

bin/dev コマンドでサーバーを起動させると foreman がインストールされ、プロジェクトディレクトリ直下にある Procfile.dev が実行されます

```bash:bin/dev
#!/usr/bin/env bash

if ! command -v foreman &>/dev/null; then
  echo "Installing foreman..."
  gem install foreman
fi

foreman start -f Procfile.dev

```

サーバーの起動と別プロセスで js と css のバンドルが行われ、yarn のコマンド使用して実行されています。

```bash:Prodfile.dev
web: bin/rails s -b '0.0.0.0' -p 3000
js: yarn build --watch
css: yarn build:css --watch
```

エラー文では error Command "build" not found 　と出ているので package.json でコマンドが設定されていないことでエラーが出ています。
コマンドを手動で追加します。
今回は css を Bootstrap を利用してますので、他の css を利用している場合はコマンドを各自書き換えてください

```json:package.json
{
  "name": "app",
  "private": "true",
  "dependencies": {
    "@hotwired/stimulus": "^3.0.1",
    "@hotwired/turbo-rails": "^7.1.0",
    "@popperjs/core": "^2.11.2",
    "autoprefixer": "^10.4.2",
    "bootstrap": "^5.1.3",
    "esbuild": "^0.14.11",
    "postcss": "^8.4.5",
    "sass": "^1.48.0",
  },
  "scripts": {
    "build:css": "sass ./app/assets/stylesheets/application.bootstrap.scss ./app/assets/builds/application.css --no-source-map --load-path=node_modules",
    "build": "esbuild app/javascript/*.* --bundle --sourcemap --outdir=app/assets/builds"
    //上記を追加する
  }
}

```

これによってサーバー起動時に bin/dev を利用することで js と css のバンドルをやってくれるようになります。

# まとめ

rail ｓ 6 の時のようにちょっとだけファイルの変更があったとしても、ほんの数秒でバンドルをしてくれるので DX がめちゃくちゃいいです。
色々と変更点が多いのでいきなりバージョンを上げるのはもう少し待ったほうが良さそうです。
