チュートリアル + アルファ を進めてみる

# チュートリアル + アルファ

- チュートリアル
    - https://learn.hashicorp.com/tutorials/terraform/infrastructure-as-code?in=terraform/azure-get-started

## 環境設定

- azsure cli を install する
- install
    - `brew install hashicorp/tap/terraform`
    - `brew install hashicorp/tap/terraform`
    - `Terraform v0.13.5`
    - `terraform -install-autocomplete`
    - vscode の pligin を install
       - `hashicorp.terraform`
- azure でリソースグループ `terra_test` を作成
    - location: `japaneast` だけを設定する
- `cat ~/ghq/github.com/github/gitignore/Terraform.gitignore >> .gitignore`

## 既存のインフラを後からコード化する
- https://dev.classmethod.jp/articles/aws-with-terraform/

- ex. recource group のマイグレーション
    - https://www.terraform.io/docs/providers/azurerm/r/resource_group.html
    - (`terraform import azurerm_resource_group.rg /subscriptions/<subscription_id>/resourceGroups/terra_test`)

- tfstateが存在する場合に、状態を確認する流れ

```
$ terraform state list
azurerm_resource_group.rg

$ terraform state show azurerm_resource_group.rg
# azurerm_resource_group.rg:
resource "azurerm_resource_group" "rg" {
    id       = "/subscriptions/<subscription_id>/resourceGroups/terra_test"
    location = "japaneast"
    name     = "terra_test"
    tags     = {}

    timeouts {}
}
```

## インフラを変更する

`main.tf` を以下のように変更する

```
resource "azurerm_resource_group" "rg" {
  name     = "terra_test"
  location = "japaneast"

  tags = {
        Environment = "Terraform Getting Started"
    }
}
```

`terraform plan -out=newplan` を実行する。  
変更箇所が分かりやすい。

```
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # azurerm_resource_group.rg will be updated in-place
  ~ resource "azurerm_resource_group" "rg" {
        id       = "/subscriptions/<subscription_id>/resourceGroups/terra_test"
        location = "japaneast"
        name     = "terra_test"
      ~ tags     = {
          + "Environment" = "Terraform Getting Started"
        }

        timeouts {}
    }

Plan: 0 to add, 1 to change, 0 to destroy.
```

- `terraform apply "newplan"` を適用する。 
    - `Apply complete! Resources: 0 added, 1 changed, 0 destroyed.` が表示される
    - `terraform state show azurerm_resource_group.rg` すると結果が変わっている

## インフラを削除する

- `terraform destroy`

- 実行するかどうかが表示される

```
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # azurerm_resource_group.rg will be destroyed
  - resource "azurerm_resource_group" "rg" {
      - id       = "/subscriptions/<subscription_id>/resourceGroups/terra_test" -> null
      - location = "japaneast" -> null
      - name     = "terra_test" -> null
      - tags     = {
          - "Environment" = "Terraform Getting Started"
        } -> null

      - timeouts {}
    }

Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value:
```
