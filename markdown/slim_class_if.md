<!--
title: 【Rails】Slimでのifを利用する方法
tags: Rails, slim, view
-->

# 初めに

Rails でポートフォリオを作成中に、条件によってクラスをつける方法を忘れてしまっていたので忘備録として残しておきます。

## 環境

Rails 7.0.2
Ruby 3.1.1
Slim 4.1.0

# 本文

erb での後置 if の書き方は多くありましたが slim でのやり方が少なかったので記事にしておきます。

こちらは erb での書き方です。

```html: if.html.erb



<div class='btn-list'>
  <% if @result == true %>
    <%= link_to "Topへ", words_path,{class:'btn-bule'} %>
  <% end %>
  <div class= "btn <%= 'btn-red' if @result == false>"></div>
</div>
```

こちらは slim での書き方です。

```slim:if.html.slim
.btn-list
  - if @result == true_
    = link_to "Topへ", words_path,class: 'btn-bule'
  div.btn class="btn" = if @result == false
```

slim では class が記述が簡単になっていますが、if を利用するときは記述しないとエラーができます。

# 参考

[公式](http://slim-lang.com/)
