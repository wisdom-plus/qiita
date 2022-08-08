<!--
title:【Rails6】Initialization autoloaded the constants ApplicationHelper を解消する
tags: Rails, Initialize, Zeitwerk
-->

# 初めに

Rails6 で Rspec を稼働させると`DEPRECATION WARNING`が出るので、それを解決します。

# 解決方法

## エラー内容

以下のエラーが発生しました。

```console
DEPRECATION WARNING: Initialization autoloaded the constants ApplicationHelper,DeviseHelper、ApplicationController、
DeviseController、Devise::SessionsController、Devise::PasswordsController、Devise::UnlocksController、Devise::RegistrationsController、
Devise::ConfirmationsController.

Being able to do this is deprecated. Autoloading during initialization is going
to be an error condition in future versions of Rails.

Reloading does not reboot the application, and therefore code executed during
initialization does not run again. So, if you reload CustomErrors, for example,
the expected changes wont be reflected in that stale Module object.

These autoloaded constants have been unloaded.

In order to autoload safely at boot time, please wrap your code in a reloader
callback this way:

    Rails.application.reloader.to_prepare do
      # Autoload classes and modules needed at boot time here.
    end

That block runs when the application boots, and every time there is a reload.
For historical reasons, it may run twice, so it has to be idempotent.

Check the "Autoloading and Reloading Constants" guide to learn more about how
Rails autoloads and reloads
(called from <top (required)> at /coffee-oma/config/environment.rb:5)

```

config/initializers から`Controllerクラス`などを呼び出しでエラーが発生します。

## 解決例

まずは、どのファイルから呼び出されているかを確認する必要があります。

今回は`ApplicationHelper`に`pp caller_locations`でどこで呼び出されているかをコンソールに出力させます。

```ruby:application_helper.rb
module ApplicationHelper
  pp caller_locations.select { |l| l.to_s.index("config/initializers") }
  # 以下省略
end
```

これでコンソールに呼び出されたファイル名が出力されるので、そのクラスを`Rails.application.reloader.to_prepare`でラップさせると解決できます。

```ruby:config/initializers/devise.rb
Rails.application.reloader.to_prepare do
  ActiveAdmin::Devise::SessionsController.class_eval do
    def after_sign_in_path_for(resource)
      if resource.is_a?(AdminUser)
        admin_root_path
      else
        root_path
      end
    end
  end
end
```

# 参考

- [Rails 6 に上げると”DEPRECATION WARNING: Initialization autoloaded the constants”のような警告が出る - Qiita](https://qiita.com/jnchito/items/b8f40e7fe82b07bc3de8)
- [定数の自動読み込みと再読み込み (Zeitwerk) - Rails ガイド](https://railsguides.jp/autoloading_and_reloading_constants.html)
