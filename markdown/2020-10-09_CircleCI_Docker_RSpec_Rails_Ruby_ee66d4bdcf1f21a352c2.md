<!--
title:   CircleCIのシステムスペックをselenium dockerを利用する
tags:    CircleCI,Docker,RSpec,Rails,Ruby
id:      ee66d4bdcf1f21a352c2
private: false
-->

今回が初投稿ですので、ミスや分かりにくいなどは多めに見てください

# 環境

・Ruby 2.7.1
・Rails 6.0.2.1

# やりたいこと

・CircleCI で selnium docker を利用してシステムテストを実行
・system js: true を CircleCI でも利用できるようにする

# docker-compose.yml

システムスペックで利用する selenium のイメージを取得し 、depens_on で連携と port の指定も忘れずに

```docker:docker-compose.yml
version: "3"
services:
  web:
    build: .
    volumes:
      - .:/myapp
    ports:
      - "3000:3000"
    tty: true
    stdin_open: true
    depends_on:
      - db
      - chrome
  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABE: db
    ports:
      - "3306:3306"
    volumes:
      - mysql-data:/var/lib/mysql
    command: --default-authentication-plugin=mysql_native_password
  chrome:
    image: selenium/standalone-chrome-debug:latest
    ports:
      - "4444:4444"
volumes:
  mysql-data:
    driver: local
```

# database.yml を修正

このままではデータベースがｄｂコンテナが利用されないので database.yml を修正します。

```ruby:database.yml
default: &default
  adapter: mysql2
  encoding: utf8mb4
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password: password
  host: db

development:
  <<: *default
  database: myapp_development

test:
  <<: *default
  database: myapp_test

production:
  <<: *default
  database: myappp_production
  username: myapp
  password: <%= ENV['MYAPP_DATABASE_PASSWORD'] %>
```

`$ docker-compose build $ docker-compose up $ docker-compose run web rake db:create`

# spec/rails_helper

テストに必要な Gem を追加

```ruby:Gemfile
group :test do
  gem 'capybara'
  gem 'selenium-webdriver'
  gem 'rspec-rails'
end
```

system テストは selenium docker を使うように rails_spec を変更する

```ruby:rails_spec.rb
Capybara.register_driver :remote_chrome do |app|
  url = 'http://chrome:4444/wd/hub'
  caps = ::Selenium::WebDriver::Remote::Capabilities.chrome(
    'goog:chromeOptions' => {
      'args' => [
        'no-sandbox',
        'headless',
        'disable-gpu',
        'window-size=1680,1050'
      ]
    }
  )
  Capybara::Selenium::Driver.new(app, browser: :remote, url: url, desired_capabilities: caps)
end

RSpec.configure do |config|
  #省略

  config.before(:each, type: :system) do
    driven_by :rack_test
  end

  config.before(:each, type: :system, js: true) do
    driven_by :remote_chrome
    Capybara.server_host = IPSocket.getaddress(Socket.gethostname)
    Capybara.server_port = 4444
    Capybara.app_host = "http://#{Capybara.server_host}:#{Capybara.server_port}"
  end
end
```

これでローカルのシステムスペックは問題なく動くはずです

# .circleci/config.yml

CircleCI の設定です

- run: mv ./config/database.yml.ci ./config/database.yml で CI 環境ではデータベースの設定を変更しています。
  name: chrome と設定しておかないと
  `Errno::EADDRINUSE:Address already in use - bind(2) for "172.27.0.3" port 4444`のエラーが発生する

```config.yml
version: 2.1
jobs:
  build:
    docker:
      - image: circleci/ruby:2.7.1-node-browsers
          RAILS_ENV: test
          DB_HOST: 127.0.0.1
      - image: mysql:8.0
        environment:
          MYSQL_ALLOW_EMPTY_PASSWORD: "true"
          MYSQL_ROOT_HOST: "127.0.0.1"
          MYSQL_DATABE: db
        command: --default-authentication-plugin=mysql_native_password
      - image: selenium/standalone-chrome-debug:latest
        name: chrome
    working_directory: ~/coffee
    steps:
      - checkout
      - run:
          name: bundleをインストール
          command: bundle check || bundle install --jobs=4
      - run:
          name: yarnを追加
          command: yarn install
      - run:
          name: webpackを追加
          command: bundle exec bin/webpack
      - run:
          name: rubocop
          command: bundle exec rubocop
      - run: mv ./config/database.yml.ci ./config/database.yml
      - run:
          name: データベースを作成
          command: bundle exec rails db:create || bundle exec rails db:migrate
      - run:
          name: rspec test
          command: |
            mkdir /tmp/test-results
            TEST_FILES="$(circleci tests glob "spec/**/*_spec.rb" | \
              circleci tests split --split-by=timings)"
            bundle exec rspec \
              --format RspecJunitFormatter \
              --out /tmp/test-results/rspec.xml \
              --format progress \
              $TEST_FILES
      - store_test_results:
          path: /tmp/test-results
      - store_artifacts:
          path: /tmp/test-results
          destination: test-results
```

これで CircleCI での rspec system テストが正常に動くはずです

# 参考

・[Rails on Docker で RSpec の System test を Selenium Docker を使ってやってみた。](https://qiita.com/at-946/items/e96eaf3f91a39d180eb3)
・[既存の Rails6 アプリを Docker 化しつつ CircleCI でシステムスペックも実行できる環境を作る](https://qiita.com/kenz-dev/items/e3d970b59bf106cab19e)
