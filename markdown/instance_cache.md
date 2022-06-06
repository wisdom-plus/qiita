<!--
title:   【Rails】DBから最新データが取得されていない時の対処法
tags:    ActiveRecord,Cache,Rails,Ruby
id:      e1347157eb6e16a7069b
private: false
-->

# 初めに

ポートフォリオのライク機能を作成した際に、`DB`のデータは変更されているのにインスタンスのデータは更新されていなかった。

## 環境

- Rails 6.1.4
- Ruby 2.7.4

## 結論

`SQLキャッシュ`がされていたので、同じ`SQL`を発行した場合キャッシュされたデータ(更新前のデータ)が返ってきた

# 解決方法

`SQLキャッシュ`は

- 同じクエリが発生すると、データベースへのクエリを実行せずに、キャッシュされた結果を返す
- `SQLキャッシュ`は、アクションの開始時に作成され、アクションの終了時に破棄されるので、アクションの実行中しか保持されない。

なので、`reload`メソッドを利用することでキャッシュを使わずに再取得することができます。

```ruby:like_controller.rb
class LikesController < ApplicationController

  def create
    review = Review.find(params[:review_id])
    like = Like.create(user: current_user, review: review)
    review.reload
    respond_to do |format|
      format.js
      format.html {redirect_to root_path}
    end
  end
end

```

これにより、`SQL`が再度発行され最新のデータが`DB`から取得されます。

# 参考

[SQL キャッシュ - Rails ガイド](https://railsguides.jp/caching_with_rails.html#sql%E3%82%AD%E3%83%A3%E3%83%83%E3%82%B7%E3%83%A5)
