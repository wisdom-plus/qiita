<!--
title:   【Rails】外部APIを使う方法
tags:    HTTP,Rails,Responce,api
id:      9c146f46db180e9469d2
private: false
-->

# 初めに

`Rails`で外部 API を利用するには、gem や`NET::HTTP`を使う方法がありますが、今回は`Ruby`にある`NET::HTTP`を使います。

# HTTP リクエストの仕方

以下は、`Net::HTTP`を利用して[PokeAPI](https://pokeapi.co/)からポケモンのデータを取得する例です。
`https://pokeapi.co/api/v2/pokemon/:id`に対して、`GET`メソッドでリクエストを送信します。
そして、json データを受け取ります。

```ruby:api_controller.rb
require 'net/http'

class ApiController < ApplicationController
  def show
    pokemon_id = params[:id]
    uri = URI.parse('https://pokeapi.co')
    # URI.parseは、URIオブジェクトを生成するメソッドです。
    http_client= Net::HTTP.new(uri.host,uri.port)
    # HTTPクライアントを生成し、引数にホスト名とポート番号を指定しています。
    get_request = Net::HTTP::Get.new("/api/v2/pokemon/#{pokemon_id}", 'Content-Type' => 'application/json')
    # Net::HTTP::Getは、HTTPのGETリクエストを表すクラスです。
    # 引数にリクエストするpathとヘッダーを指定しています。
    http_client.use_ssl = true
    # httpsで通信をする場合はuse_sslをtrueにする必要がある
    response = http_client.request(get_request)
    # requestメソッドの引数にNet:HTTP:Responseオブジェクトをあたえます。
    # responseには、HTTPレスポンスが格納されている
    @data = JSON.parse(response.body)
    # @responseはJSON形式になっているので、JSON.parseでHashに変換する必要がある
  end
end

```

# 参考

- [Net::HTTP::Get - Ruby リファレンス](https://docs.ruby-lang.org/ja/latest/class/Net=3a=3aHTTP=3a=3aGet.html)
- [Net::HTTP - Ruby リファレンス](https://docs.ruby-lang.org/ja/latest/class/Net=3a=3aHTTP.html)
- [pokeAPI](https://pokeapi.co/docs/v2)
