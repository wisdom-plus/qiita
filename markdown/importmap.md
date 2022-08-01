<!--
title:   【Rails】importmap-railsの注意点
tags:    Importmap.Javascript,Rails,Rails7
id:      af1c2aa655839e9c83e8
private: false
-->


# 初めに

Rails7 では、`importmap-rails`が標準で利用できるようになりました。

`webpacker`や`jsbuilding-rails`などと違い、javascript モジュールをブラウザに直接インポートされます。

`importmap-rails`は他と違い注意しないといけない点があるので、忘備録として記事にしておきます。

# 注意点

## パッケージの追加の仕方

`importmap`は javascript パッケージを追加するためには、以下のようにします。

```console
$ bin/importmap pin [パッケージ名]
Pinning [パッケージ名] to [パッケージURL]
```

といった形で、パッケージを追加していきます。
`importmap`で利用できるのは npm から取得できるパッケージで JavaScript CDN 経由で利用できるものだけです。

## JSX は利用できない

`webpack`のようにコンパイルなどを行わなず、ブラウザに直接インポートされるので`JSX`のような明示的なトランスパイルやコンパイルのステップが必要である場合は`importmap-rails`では利用できない

## パッケージのアップデート

`npm update`などのようにパッケージをまとめてアップデートすることができませんが、下記のようなコマンドを使うことで新しいバージョンが存在するかを確認することができます。

```console
$ ./bin/importmap outdated
新しいバージョンのnpmレジストリをチェックします

$ ./bin/importmap audit
既知のセキュリティ問題についてnpmレジストリをチェックします
```

## パッケージをローカルにインストールする

`--download`オブションを指定することで、パッケージをローカルにインストールできます。

```console
bin/importmap pin [パッケージ名] --download
```

`vender/javascript`ディレクトリにインストールされる。

# 参考

- [importmap-rails - github](https://github.com/rails/importmap-rails)
- [Rails 7: importmap-rails gem README（翻訳） - TechRacho](https://techracho.bpsinc.jp/hachi8833/2022_06_29/112183)