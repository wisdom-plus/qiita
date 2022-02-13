<!--
title:   Railsでvpc endpointを利用してsesからメールを送信する
tags:    AWS,Rails,ses,vpcendpoint
id:      dca628fb985b3d881372
private: false
-->

# 環境

- Ruby 2.7.2
- Rails 6.1.3
- terraform 1.0.2

# やりたいこと

- private subnet 内にある ECS fargate から AWS SES を利用してメールを送信する

# VPC endpoint

private subnet 内に ecs インスタンスを作った場合 NAT gatway などを利用しないと AWS SES に接続できません。

ただし、private subnet 内に VPC endpoint を設置することで AWS サービスとプライベートにアクセスができます。

現状、ses の vpc endpoint は ses-smtp-endpoint のみが対応してます。

# Rails

aws 公式で aws-adk-rails gem がありますが、SES との接続は HTTPS を利用しているので vpc endpoint を利用して接続できません。ですので、VPC endpoint を利用して SES からメールを送信するには action_mailer の smtp 設定を行うことで通信することができます。

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

`ruby:.env AWS_DOMAIN = #SESに紐付いているドメイン名 AWS_SMTP_ACCESS_KEY_ID=#SMTP認証で得たアクセスキー AMS_SMTP_SECRET_ACCESS_KEY=#SMTP認証で得たシークレットキー `

これで SES の SMTP 　 VPC 　 endpoint を利用してメールを送信できます。

# 参考

[【Rails】ActionMailer + AWS SES](https://qiita.com/syukan3/items/7f33a8cbd3c0301cd6f1)
[Amazon SES SMTP 認証情報を取得](https://docs.aws.amazon.com/ja_jp/ses/latest/DeveloperGuide/smtp-credentials.html)
