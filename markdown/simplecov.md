<!--
title:   【Rails】Railsでカバレッジを計測する
tags:    Rails,Ruby,SimpleCov
id:      404725e23a5398dd921c
private: false
-->


# 初めに

`Rails6`のプロジェクトにカバレッジを導入したので、そのやり方を記事にしておきます。

## 環境

- Rails 6.1.4
- Ruby 2.7.4
- simplecov 0.21.2

# simplecov を導入する

カバレッジを測定するためのジェムである`simplecov`を追加します。

```ruby:Gemfile
group :test do
  gem 'simplecov', require: false
end
```

`simplecov`の設定を追加する

```ruby:spec/rails_helper.rb
require 'spec_helper'
require 'simplecov'
SimpleCov.start 'rails'


RSpec.configure do |config|
# 省略
end
```

これで`Rspec`実行後に、`coverage/index.html`に実行結果が出力されます。

# 参考

[simplecov - Github](https://github.com/simplecov-ruby/simplecov)