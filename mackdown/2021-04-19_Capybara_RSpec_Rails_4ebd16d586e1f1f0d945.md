<!--
title:   system_specで直接javascriptを実行する
tags:    Capybara,RSpec,Rails
id:      4ebd16d586e1f1f0d945
private: false
-->

# はじめに

system spec を書いてるときに、javascript が起動しないときがあり直接 javascript を実行できたらエラーを回避できるときがあると思います。

# 解決法

capybara の execute_script を使うと javascript を実行させることができます。

```ruby
page.excute_script "console.log('javascript')"
```

これを利用すると、強引にフォームに値を送り込めます。

```ruby
page.excute_script "$('#form').val('フォームの値')"
```

本来は、エラーを解消してテストをパスするべきなので利用はご利用的に
