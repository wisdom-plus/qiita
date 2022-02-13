<!--
title:   system_specで直接javascriptを実行する
tags:    Capybara,RSpec,Rails
id:      4ebd16d586e1f1f0d945
private: false
-->
#はじめに
system specを書いてるときに、javascriptが起動しないときがあり直接javascriptを実行できたらエラーを回避できるときがあると思います。
#解決法
capybaraのexecute_scriptを使うとjavascriptを実行させることができます。

```ruby
page.excute_script "console.log('javascript')"
```

これを利用すると、強引にフォームに値を送り込めます。

```ruby
page.excute_script "$('#form').val('フォームの値')"
```

本来は、エラーを解消してテストをパスするべきなので利用はご利用的に