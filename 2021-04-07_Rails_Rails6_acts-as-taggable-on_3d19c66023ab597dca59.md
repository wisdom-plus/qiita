<!--
title:   【Rails】acts-as-taggable-onの区切り文字の変更
tags:    Rails,Rails6,acts-as-taggable-on
id:      3d19c66023ab597dca59
private: false
-->
* タグ機能を実装時にてまどったので備忘録として、残しておきます

#環境
* Rails 6.1.3
* acts-as-taggable-on 7.0.0

#デフォルト
デフォルトでは`,`が区切りとなり、複数のタグに分割してくれる。

```ruby:例)デフォルト
class User < ActiveRecord::Base
  acts_as_taggable_on :tags
end
tag = ['ruby,javascript,react']
@user = User.create(tag_list: tag)
@user.tag_list = ['ruby','javascript','react']
```
#カスタマイズ
区切り文字を変更するのに前までは、メソッドを書いて処理していましたがgithubのREADMEに変更方法を書いてありました。

```ruby
ActsAsTaggableOn.delimiter = ',' 
# ','を変更することで区切り文字を変更できる
# [',' , '|']のように配列で区切り文字を渡すことができます
```
config/initializers/tag.rbを作成し、その中に書く

```ruby:config/initializers/tag.rb
ActsAsTaggableOn.delimiter = ' '
# これにより半角の空白で文字が区切られます
```

ちなみに、タグを追加するメソッドtag_list.add('文字列')　のときは分割はしてくれません

```ruby
tag_str = 'html css'
@user.tag_list.add(add_tag, parse: true)
@user.tag_list = ['ruby','javascript','react','html','css']
# parse: trueをつけることで区切り文字で分割してくれます
```

#まとめ
公式を確認することで解決できることがあるので、まずは公式を確認しましょう。