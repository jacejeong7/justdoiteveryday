# jdi terraform on aws & etc

날짜: 2023년 1월 17일

# git

## delete commit

```bash
* 이력을 남기고 원복 하기 위해서는 revert 사용 

* 제거 커밋 바로 이전 commit id 입력 

(base) ➜  17 git:(main) ✗ git reset --hard 8d069f5bbede2fcf7d10d16a5cf072c2b8eb8f03
HEAD is now at 8d069f5 230113
```

### 참고

[[Github] git commit 취소/삭제하기](https://doqtqu.tistory.com/234)

## ignore

### 설정

```bash
git 최상위 폴더에서 .gitignore 파일 작성 

---------------

# Local .terraform directories
**/.terraform/*

# .tfstate files
*.tfstate
*.tfstate.*

# Crash log files
crash.log
crash.*.log

# Exclude all .tfvars files, which are likely to contain sensitive data, such as
# password, private keys, and other secrets. These should not be part of version 
# control as they are data points which are potentially sensitive and subject 
# to change depending on the environment.
*.tfvars
*.tfvars.json

# Ignore override files as they are usually used to override resources locally and so
# are not checked in
override.tf
override.tf.json
*_override.tf
*_override.tf.json

# Include override files you do wish to add to version control using negated pattern
# !example_override.tf

# Include tfplan files to ignore the plan output of command: terraform plan -out=tfplan
# example: *tfplan*

# Ignore CLI configuration files
.terraformrc
terraform.rc
```

[gitignore/Terraform.gitignore at main · github/gitignore](https://github.com/github/gitignore/blob/main/Terraform.gitignore)

### 적용결과

```bash
(base) ➜  17 git:(main) ✗ git add ./*
(base) ➜  17 git:(main) ✗ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   README.md
        new file:   learn-terraform-aws-instance/.terraform.lock.hcl
        new file:   learn-terraform-aws-instance/main.tf
        new file:   learn-terraform-aws-instance/variables.tf

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        ../../../.gitignore
        ../16/.Rhistory
```

대용량 파일 제거 

```bash
(base) ➜  17 git:(main) ✗ du -ah
4.0K    ./README.md
8.0K    ./learn-terraform-aws-instance/terraform.tfstate.backup
4.0K    ./learn-terraform-aws-instance/terraform.tfstate
4.0K    ./learn-terraform-aws-instance/main.tf
381M    ./learn-terraform-aws-instance/.terraform/providers/registry.terraform.io/hashicorp/aws/4.50.0/darwin_amd64/terraform-provider-aws_v4.50.0_x5
381M    ./learn-terraform-aws-instance/.terraform/providers/registry.terraform.io/hashicorp/aws/4.50.0/darwin_amd64
381M    ./learn-terraform-aws-instance/.terraform/providers/registry.terraform.io/hashicorp/aws/4.50.0
381M    ./learn-terraform-aws-instance/.terraform/providers/registry.terraform.io/hashicorp/aws
381M    ./learn-terraform-aws-instance/.terraform/providers/registry.terraform.io/hashicorp
381M    ./learn-terraform-aws-instance/.terraform/providers/registry.terraform.io
381M    ./learn-terraform-aws-instance/.terraform/providers
381M    ./learn-terraform-aws-instance/.terraform
4.0K    ./learn-terraform-aws-instance/variables.tf
4.0K    ./learn-terraform-aws-instance/.terraform.lock.hcl
381M    ./learn-terraform-aws-instance
381M    .
```

---

# Terraform

terraform apply on aws 

[Build Infrastructure | Terraform | HashiCorp Developer](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/aws-build)

- basic commands

```bash

terraform init 

terraform fmt 

terraform validate 

terraform apply 

terraform show 

terraform state list 

terraform destroy 
```

- modify

```bash

- ami-0fe95ba7739b18598 
+ ami-08d235cd28b373655

- t2.micro
+ t2.large 

terraform apply 

.... 

Plan: 1 to add, 0 to change, 1 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_instance.app_server: Destroying... [id=i-0d70bbc4b1fe288b2]
aws_instance.app_server: Still destroying... [id=i-0d70bbc4b1fe288b2, 10s elapsed]
aws_instance.app_server: Still destroying... [id=i-0d70bbc4b1fe288b2, 20s elapsed]
aws_instance.app_server: Still destroying... [id=i-0d70bbc4b1fe288b2, 30s elapsed]
aws_instance.app_server: Destruction complete after 30s
aws_instance.app_server: Creating...
aws_instance.app_server: Still creating... [10s elapsed]
aws_instance.app_server: Still creating... [20s elapsed]
aws_instance.app_server: Still creating... [30s elapsed]
aws_instance.app_server: Creation complete after 32s [id=i-0e8a8a2df8418232a]

Apply complete! Resources: 1 added, 0 changed, 1 destroyed.
```

- define input variables

- [variables.tf](http://variables.tf)  (기존 값 변수로 대체)

```bash
tags = {
-    Name = "ExampleAppServerInstance"
+    Name = var.instance_name
   }
```

```bash
variable "instance_name" {
  description = "Value of the Name tag for the EC2 instance"
  type        = string
  default     = "ExampleAppServerInstance"
}
```

```bash
terraform apply -var "instance_name=YetAnotherName"

(base) ➜  learn-terraform-aws-instance git:(main) ✗ terraform apply -var "instance_name=YetAnotherName"
aws_instance.app_server: Refreshing state... [id=i-0e8a8a2df8418232a]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # aws_instance.app_server will be updated in-place
  ~ resource "aws_instance" "app_server" {
        id                                   = "i-0e8a8a2df8418232a"
      ~ tags                                 = {
          ~ "Name" = "ExampleAppServerInstance" -> "YetAnotherName"
        }
      ~ tags_all                             = {
          ~ "Name" = "ExampleAppServerInstance" -> "YetAnotherName"
        }
        # (29 unchanged attributes hidden)

        # (8 unchanged blocks hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_instance.app_server: Modifying... [id=i-0e8a8a2df8418232a]
aws_instance.app_server: Modifications complete after 1s [id=i-0e8a8a2df8418232a]

Apply complete! Resources: 0 added, 1 changed, 0 destroyed.

```

---
