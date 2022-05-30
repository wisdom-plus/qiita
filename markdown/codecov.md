<!--
title:   【Rails】Rspecのカバレッジをプルリクのコメントで表示する
tags:    CircleCI,Rails,Ruby,SimpleCov,codecov
id:      183abf1059ad5b2e860c
private: false
-->

# 初めに

`OSS`などでよく見る`CI`での Rspec カバレッジをプルリクのコメントで表示するやり方を記事にしておきます。

## 環境

- Rails 6.1.4
- Ruby 2.7.4
- codecov 0.6.0
- simplecov 0.21.2

# 本編

今回は`CircleCI` `Codecov` `simplecov`を利用してカバレッジを取得していきます。

## Codecov の設定

1. まずは、[Codecov](https://about.codecov.io/sign-up/)にアクセスし、アカウントを作成してください。
2. リポジトリの設定ページにアクセスし、`Codecov Token`を取得します。

## CircleCI の設定

すでに`CircleCI`での Rspec が稼働している前提です。

1. `CircleCI`の環境変数の設定ページにアクセスする。
2. `CODECOV_TOKEN`の名前で先ほど取得したトークンを設定します。
3. orb を利用するので、`Organization Settings`の`Security`セクションにアクセスします。
4. `Orb Security Settings`のセクションから`Yes`を選択する。

## Gem の追加

カバレッジを計測するための Gem と`Codecov`のための Gem を追加する。

```ruby
group :test do
  gem 'codecov',   require: false
  gem 'simplecov', require: false
end
```

`Rspec`の設定に`simplecov`と`codecov`の設定を追加する。

```ruby:spec/rails_helper.rb
require 'spec_helper'
require 'codecov'
require 'simplecov'
SimpleCov.start 'rails'
SimpleCov.formatter = SimpleCov::Formatter::Codecov

RSpec.configure do |config|
# 以下省略
end
```

## CircleCI の設定を行う

`.circleci/config.yml`に`orbs`を追加する。

```yaml:.circleci/config.yml

version: 2.1

orbs:
  codecov: codecov/codecov@1.0.5

jobs:
  build:
    steps:
      - setup
      - checkout
      - run:
        # Rspecを稼働させる
      - store_test_results: # coverageディレクトリが消えないようにする
          path: ./coverage
      - codecov/upload: # カバレッジの結果のファイルを送信する
          file: ./coverage/codecov-result.json

```

これで`Codecov`にテストのカバレッジが表示されるようになります。
そして、`CI`が稼働してテストが成功するとプルリクにカバレッジ結果がコメントとして表示されるようになります。

# 参考

- [公式ドキュメント - CircirCI](https://circleci.com/docs/ja/2.0/code-coverage/)
- [codecov - CircleCI orbs](https://circleci.com/developer/ja/orbs/orb/codecov/codecov)
- [Quick Start - codecov](https://docs.codecov.com/docs/quick-start)
