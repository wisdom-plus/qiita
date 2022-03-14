<!--
title: 【Rspec】turbo_streamを使っているときのrequest specの書き方
tags: Rails7, Ruby, Rspec, request_spec
 -->

# 　はじめに

Rails7 からはバリデーションの失敗時は `status: :unprocessable_entity` をつける必要があります。
しかし request spec で書いている時、バリデーション失敗時でにリクエストのステータスが `200` で帰ってきてしまう

## やりたい事

バリデーション失敗時のレスポンスのステータスが `422`になるようにする

## 環境

- Rails 7.0.22
- Ruby 3.1.1
- rspec-rails 5.1.0

# request spec

Rails7 から form の送信はデフォルトで Turbo_stream を利用するようになりました。
turbo_stream 利用時はレスポンスのヘッダーに `Accept: 'text/vnd.turbo-stream.html'` が追加されるので、spec の送信時にもヘッダーを追加する必要があります。

```ruby:rails/spec/request/user.rb
require 'rails_helper'

RSpec.describe 'Registrations', type: :request do
  let(:user) { create(:user) }

  describe 'POST /users' do
    # 省略
    context 'failure' do
      it 'リクエストが失敗(nameが空)' do
        post user_registration_path, params:
        { user: {
          name: '',
          email: 'test@example.com',
          password: 'password',
          password_confirmation: 'password' } },
        xhr: true, headers: { Accept: 'text/vnd.turbo-stream.html' } #この行を追加する
      end

    end
  end

end


```

# 参考

参考をなった記事たち

[Rails 7.0 + Ruby 3.1 でゼロからアプリを作ってみたときにハマったところあれこれ](https://qiita.com/jnchito/items/5c41a7031404c313da1f#%E3%83%90%E3%83%AA%E3%83%87%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E5%A4%B1%E6%95%97%E6%99%82%E3%81%AB-status-unprocessable_entity-%E3%82%92%E4%BB%98%E3%81%91%E3%82%8B%E5%BF%85%E8%A6%81%E3%81%8C%E3%81%82%E3%82%8B)

[Rails7 で Devise を利用する方法](https://gorails.com/episodes/devise-hotwire-turbo)
