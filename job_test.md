<!--
title:【Rails】Active Jobのテストの書き方
tags: Rails,Rspec,Active Job,system_spec
-->

# 初めに

Active job のテストは E2E テストですることができますが、job 単体のテストをしたい時に書き方がわからなかったのでここに残しておきます。

## やりたい事

job 単体のテストを書く

## 環境

Ruby 2.7.4

Rails 6.1.3

Rspec 3.10.1

# Job テスト

まずは、spec ディレクトリの下に jobs ディレクトリを作成します。
そして、テストしたい job のスペックを作成します。

```console:コンソール

/rails # mkdrir spec/jobs

/rails # touch spec/jobs/<job名>_job__spec.rb

```

job がキューに入ったことの確認するテストと job が実行されているかのテストを書きます。
DeleteReviewJob は review を削除する job です

```ruby:rails/app/jobs/delete_review_job.rb
class DeleteReviewJob < ApplicationJob
  queue_as :default

  def perform(review)
    review.destroy
  end
end

```

```ruby:rails/spec/jobs/job名_job_spec.rb
require 'rails_helper'

RSpec.describe 'jobs', type: :job do
  let(:user) { create(:user) }
  let(:product) { create(:product, user: user) }
  let(:review) { create(:review, user: user, product: product) }

  describe 'perform_later' do
    it 'enqueue job' do
      ActiveJob::Base.queue_adapter = :test #jobを起動するアダプターtest環境ではtestにしておく
      DeleteReviewJob.perform_later(review)
      expect(DeleteReviewJob).to have_been_enqueued #キューに入ったかを確認する
    end

    it 'delete review' do
      ActiveJob::Base.queue_adapter = :test
      review
      expect do
        DeleteReviewJob.perform_now(review)
      end.to change(Review, :count).by(-1) #jobが発動して、reviewが削除されたかを確認している
    end
  end
end

```

# 参考

[Relish](https://relishapp.com/rspec/rspec-rails/docs/job-specs/job-spec)

[RSpec でキューイングした ActiveJob を同期実行する](https://qiita.com/upinetree/items/41a2a8fe9e1dd7c291ab)

[blog.kotamiyake.me](https://blog.kotamiyake.me/tech/rails-activejob-rspec-tips/)
