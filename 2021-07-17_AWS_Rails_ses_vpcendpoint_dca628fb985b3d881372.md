<!--
title:   Railsでvpc endpointを利用してsesからメールを送信する
tags:    AWS,Rails,ses,vpcendpoint
id:      dca628fb985b3d881372
private: false
-->
#環境

+ Ruby 2.7.2
+ Rails 6.1.3
+ terraform 1.0.2


#やりたいこと
+ private subnet内にあるECS fargateからAWS SESを利用してメールを送信する

#VPC endpoint
private subnet内にecsインスタンスを作った場合NAT gatwayなどを利用しないとAWS SESに接続できません。

ただし、private subnet内にVPC endpointを設置することでAWSサービスとプライベートにアクセスができます。

現状、sesのvpc endpointはses-smtp-endpointのみが対応してます。

#Rails
aws公式でaws-adk-rails gemがありますが、SESとの接続はHTTPSを利用しているのでvpc endpointを利用して接続できません。ですので、VPC endpointを利用してSESからメールを送信するにはaction_mailerのsmtp設定を行うことで通信することができます。

```ruby:config/environments/production.rb
  config.action_mailer.default_url_options = { host: ENV['AWS_DOMAIN'] }
  config.action_mailer.delivery_method = :smtp
  config.action_mailer.perform_caching = true
  ActionMailer::Base.smtp_settings = {
      :address =>        "email-smtp.ap-northeast-1.amazonaws.com",
      :port =>           587,
      :domain =>         ENV['AWS_DOMAIN']
      :authentication => :login,
      :user_name =>      ENV['AWS_SMTP_ACCESS_KEY_ID'],
      :password =>       ENV['AWS_SMTP_SECRET_ACCESS_KEY'],
  }
```

`ruby:.env
AWS_DOMAIN = #SESに紐付いているドメイン名
AWS_SMTP_ACCESS_KEY_ID=#SMTP認証で得たアクセスキー
AMS_SMTP_SECRET_ACCESS_KEY=#SMTP認証で得たシークレットキー
`

これでSESのSMTP　VPC　endpointを利用してメールを送信できます。

#参考
[【Rails】ActionMailer + AWS SES](https://qiita.com/syukan3/items/7f33a8cbd3c0301cd6f1)
[Amazon SES SMTP 認証情報を取得](https://docs.aws.amazon.com/ja_jp/ses/latest/DeveloperGuide/smtp-credentials.html)