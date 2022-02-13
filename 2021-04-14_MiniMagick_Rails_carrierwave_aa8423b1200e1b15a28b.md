<!--
title:   carrierwave+Minimagickで利用する、画像リサイズメソッド
tags:    MiniMagick,Rails,carrierwave
id:      aa8423b1200e1b15a28b
private: false
-->

# はじめに

ポートフォリオの一覧ページを作成する際に、サイズが違うときれいに並ばないので画像のサイズを投稿時に変更するようにしました。
その際、使うメソッドを迷ったので書いておきます。

種類は４つです。
・ resize_to_fit
・ resize_to_limit
・ resize_to_fill
・ resize_and_pad

# minimagick を導入

まず最初に minimagick を利用するには、imagemagick というパッケージをインストールする必要があります。
docker の alpine を使っているので、apk でインストールします。
Mac 使っている人は brew でインストールしてください。

```ruby:コンソール
apk add imagemagick
```

続いて、Gemfile に minimagick を記載し、bundle install を実行します

```ruby:Gemfile
gem 'minimagick', '~> 4.0'
```

最後に uploader.rb の include CarrierWave::MiniMagick のコメントを外す

```ruby:app/uploaders/image_uploader.rb
class ImageurlUploader < CarrierWave::Uploader::Base
  # Include RMagick or MiniMagick support:
  # include CarrierWave::RMagick
  include CarrierWave::MiniMagick  # ←コメントを外す

　(省略)

end
```

これで画像をリサイズすることができるようになります。

# resize_to_fit

これが基本的なメソッドです。
縦横比を保持しながら、引数で指定したサイズを変更します

```ruby:app/uploaders/image_uploader.rb
process resize_to_fit: [width,height]
```

# resize_to_limit

縦横比を保持しながら、引数で指定したサイズに収まるようにサイズを変更します。
ただし、引数よりサイズが小さい場合はサイズを変更しない。

```ruby:app/uploaders/image_uploader.rb
process resize_to_limit: [width,height]
```

# resize_to_fill

元画像から第３引数を中心として第１・２引数ののサイズに切り抜きを行う。
縦横比は変更される。

```ruby:app/uploaders/image_uploader.rb
process resize_to: [width,height,gravity]
#(gravity: デフォルトは 'Center'; オプション： 'NorthWest'、 'North'、 'NorthEast'、 'West'、 'C​​enter'、 'East'、 'SouthWest'、 'South '、'SouthEast'）
```

# resize_and_pad

縦横比を保持しながら、引数で指定したサイズに収まるように画像のサイズを変更する。
余白部分の第三引数の色で塗りつぶす

```ruby:app/uploaders/image_uploader.rb
process resize_to: [width,height,background,gravity]
#(background: 'ffffff'のように１６進コードで指定)
#(デフォルトはgif・pngの場合は透明、jpegは白)
```

# 参考

[Github Carrirwave](https://github.com/carrierwaveuploader/carrierwave)
[Github Minimagick](https://github.com/minimagick/minimagick)
[紹介したメソッドのソースコード](https://github.com/carrierwaveuploader/carrierwave/blob/master/lib/carrierwave/processing/mini_magick.rb)
