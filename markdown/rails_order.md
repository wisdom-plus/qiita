<!--
title:   【Rails】レコードを配列の順番に並び替える
tags:    ActiveRecord,Rails,Ruby
id:      d28d35c918fa4e63588f
private: false
-->

# 初めに

## 環境

Rails 7.0.2
Ruby 3.1.1

## やりたいこと

配列の順番にレコードを並び替える。

# 本文

通常 where に配列を渡して、レコードを取得すると id 順に並び替えられて取得されます。
ですが`in_order_of`を使用することで配列の順番に並び替えられます。

```ruby
arr = [11,2,4,6,8,1]

users = User.where(id: arr)
# puts users.ids -> [1,2,4,6,8,11]


users = User.where(id: arr).in_order_of(:id ,arr)
# puts users.ids -> [11,2,4,6,8,1]
```

ちなみに `Rails 7.0.0`になってから追加されたメソッドでデータベースが `Mysql` や `postgresql` でも関係なく動きます。

# 参考

[in_order_of - Rails ドキュメント](https://railsdoc.com/page/in_order_of)

[ソースコード - Github](https://github.com/rails/rails/blob/984c3ef2775781d47efa9f541ce570daa2434a80/activerecord/lib/active_record/relation/query_methods.rb#L429)
