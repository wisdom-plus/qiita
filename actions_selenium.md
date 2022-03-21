<!--
title: 【Github actions】Github Actionsで発生したSelenium::WebDriver::Error::UnknownError:unknown error: net::ERR_NAME_NOT_RESOLVEDを解消する
tags: Githubactions,selenium,Rails,Rspec,system spec
-->

# 初めに

Github actions で Rails の Rspec の system test を行なっている際に Selenium::WebDriver::Error::UnknownError:unknown error: net::ERR_NAME_NOT_RESOLVED が発生しました。

## やりたい事

`Selenium::WebDriver::Error::UnknownError:unknown error: net::ERR_NAME_NOT_RESOLVED`を解消する

## 環境

- Rails 7.0.22
- Ruby 3.1.1
- Rspec 3.10.0

# workflow

github actions 上で selenium docker を利用して、system spec を動かした際にエラーが発生

`Selenium::WebDriver::Error::UnknownError:unknown error: net::ERR_NAME_NOT_RESOLVED`
エラーメッセージに内容は名前が解消していないと出ています。

## rspec.yml

各コンテナの名前の名前を設定行います。
大切なのは Rails を実行しているコンテナの名前の設定がないとエラーが発生します。

```yaml:.github/workflows/rspec.yml
name: Rspec
on:
  push:
    paths-ignore:
      - README.md
      - Dockerfile
      - docker-compose.yml

jobs:
  rspec:
    runs-on: ubuntu-20.04
    env:
      RAILS_ENV: test
    services:
      database: #postgresコンテナの名前
        image: postgres:14.1
        ports:
          - 5432:5432
        env:
          POSTGRES_PASSWORD: password
          POSTGRES_USER: postgres
          POSTGRES_DB: test
        options: --health-cmd pg_isready --health-interval 10s --health-timeout 5s --health-retries 5
      chrome: #seleniumコンテナの名前
        image: selenium/standalone-chrome
        ports:
          - 4444:4444
    container:
      image: ruby:3.1-alpine
      options: --name web #railsコンテナの名前を設定する
      ports:
        - 3000:3000
    steps:
      # 省略
```

# 参考

[コンテナ内でのジョブの実行](https://docs.github.com/ja/actions/using-jobs/running-jobs-in-a-container)

[container の options の一覧](https://docs.docker.com/engine/reference/commandline/create/#options)
