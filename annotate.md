<!--
title: 【Rails7】annotateのrouterが動かない
tags: Rails,Ruby,Gem,annotate
 -->

# 　初めに

Rails の便利な Gem で annotate というものがあります。
データベースのスキーマと rotuer をファイルにコメントとして記述してくれる Gem です。
これのおかげで、確認のするための時間が減ります。
しかし、ここ最近更新がなく Rails7 に対応していない状態です。

## やりたい事

Rails7 でも annotate の router が動くように修正する

## 環境

- Ruby 3.1.1
- Rails 7.0.2.2
- annotate 3.2.0

# 解消法

## 原因

Rails6 から rake routes が廃止され、rails routes でルート一覧が出るようになりました。
Gem の内部のコードで rake routes が使われているので、そこを書き換えると動くようになります。

## Gem のソースコード

annotate のソースを見るために

`$ gem which annotate`

を実行し、コードの場所を特定します。

```console:コンソール
/rails # gem which annotate

/usr/local/bundle/gems/annotate-3.2.0/lib/annotate.rb

/rails # cd /usr/local/bundle/gems/annotate-3.2.0/lib/annotate/annotate_routes

/usr/local/bundle/gems/annotate-3.2.0/lib/annotate/annotate_routes  # cat header_generator.rb

```

ソースコードの`rake routes`を `rails routes` に書き換えると `annotate -routes` が動くようになります。

```ruby:header_generator.rb
require_relative './helpers'

module AnnotateRoutes
  class HeaderGenerator
    class << self
      # 省略

      def routes_map(options)
        #result = `rake routes`.chomp("\n").split(/\n/, -1)
        result = `rails routes`.chomp("\n").split(/\n/, -1) #ここのrakeをrailsに書き換えるだけ

        # In old versions of Rake, the first line of output was the cwd.  Not so
        # much in newer ones.  We ditch that line if it exists, and if not, we
        # keep the line around.
        result.shift if result.first =~ %r{^\(in \/}

        ignore_routes = options[:ignore_routes]
        regexp_for_ignoring_routes = ignore_routes ? /#{ignore_routes}/ : nil

        # Skip routes which match given regex
        # Note: it matches the complete line (route_name, path, controller/action)
        if regexp_for_ignoring_routes
          result.reject { |line| line =~ regexp_for_ignoring_routes }
        else
          result
        end
      end
    end

  end
end

```

# 参考

修正のプルリクエストがあるのだが、未だマージされていない便利なジェムなのでこのまま無くなるのは惜しい。

[プルリクエスト](https://github.com/ctran/annotate_models/pull/843)
