<!--
title:   【Rails】actionmeilerのsubjectをi18nで日本語化する
tags:    I18n,Rails,Ruby
id:      ad8a33aac08010768dbe
private: false
-->


# 初めに

`Rails`でメールの件名を `i18n` で設定する方法を忘備録として残しておきます。

## 環境

- Rails 6.1.4

- Ruby 2.7.4

# 日本語設定

`ja.yml`に日本語を追加することで`subject`を設定できます。
また、引数を与えて動的に変更することもできます。

## user_mailer.rb

```ruby:user_mailer.rb
class UserMailer < ActionMailer::Base
  def welcome
    # mail(to: '宛先' ,suject: '件名')
    mail(to: '宛先')
  end

  def signin(user)
    mail(to:'宛先', subject: default_i18n_subject(user: user.name))
    # 引数を与える場合はdefault_i18n_subjectを利用する
  end
end

```

## ja.yml

```yaml:ja.yml
ja:
  user_mailer:
    welcome:
      subject: '件名'
    signin:
      subject: "%{user}さん, ようこそ！"

```

# まとめ

日本語だけならこのような対応は必要ないですが、英語などの多言語に対応するときに必要になるので覚えておく必要があります。

## 参考

[[初学者]Rails の i18n による日本語化対応](https://qiita.com/shimadama/items/7e5c3d75c9a9f51abdd5#%E5%BC%95%E6%95%B0%E3%81%82%E3%82%8A)

[Rails 国際化(i18n) API - Rails ガイド](https://railsguides.jp/i18n.html)