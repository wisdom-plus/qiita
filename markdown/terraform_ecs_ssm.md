<!--
title:【Terraform】ECSの環境変数をSSMを利用して設定する
tags: terraform, AWS, ecs, ssm
-->

# 初めに

現在作成中のポートフォリオは、`Terraform`を利用して`AWS`にデプロイをしています。

`ECS`のコンテナに対して、`enviroment`から環境変数を渡していましたが変数の追加などがあった際、タスク定義を書き換える必要がありめんどくさいです。

`SSM`を通して環境変数を渡すことでタスク定義を書き換えずに更新できます。

## 環境

- terraform v1.0.2
- provider AWS v3.38.0

# 環境変数を SSM に登録する

`task definition`の`secretsのvalueFrom`に`SSMのarn`を追加する必要があります。

まずは`ssm_parameter`を作成します。

変数自体は`terraform.tfvars`に書きます。

```terraform:ssm.tf
resource "aws_ssm_parameter" "secret" {
  name = "ENV_FILE"
  type = "SecureString"
  value = var.env_file
}
```

# 利用するタスク定義を書き換える

`ecs_task definition`を書き換えます。

`template_file`を利用しているので、`vars`の`ssm_parameter_arn`に`aws_ssm_parameter`の`arn`を書きます。

```terraform:ecs.tf
resource "aws_ecs_task_definition" "ecs-task" { #タスク定義
  family                   = "ecs-task"
  cpu                      = "512"
  memory                   = "1024"
  network_mode             = "awsvpc"
  requires_compatibilities = ["FARGATE"]
  container_definitions    = data.template_file.rails_task.rendered
}

data "template_file" "rails_task" {
  template = file("${path.module}/task/rails_container_definitions.json")

  vars = {
    ssm_parameter_arn = aws_ssm_parameter.secret.arn,
    rails_image = var.rails_image,
  }
}
```

```json:task_definitions.json
[
  {
    "name": "rails",
    "image": "${rails_image}",
    "environment": [
      {
        "name": "RAILS_ENV",
        "value": "production"
      }
    ],
    "secrets": [
      {
        "name": "ENV_FILE",
        "valueFrom": "${ssm_parameter_arn}"
      }
    ],
    // 以下省略
  }
]
```

これにより、`ssm_paramter`で設定した`ENV_FILE`を`rails`のコンテナに渡すことができます。

環境変数を変更したい場合は、`terraform.tfvars`を書き換え`terraform apply`をすることで更新するできます。

# 参考

- [ssm_parameter - Terraform Documentation](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/ssm_parameter)
- [aws_ecs_task_definition - Terraform Documentation](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/ecs_task_definition)
- [タスク定義パラメータ - AWS ドキュメント](https://docs.aws.amazon.com/ja_jp/AmazonECS/latest/developerguide/task_definition_parameters.html)
