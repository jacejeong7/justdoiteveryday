



[Terraform remote state](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/aws-remote)

[image:./img/t_remote.png]


```
terraform {
	  cloud {
		      organization = "jacejeong7"
				      workspaces {
						        name = "learn-tfc-aws"
									    }
			    }
	    required_providers {
			    aws = {
					      source  = "hashicorp/aws"
							        version = "~> 4.16"
									    }
				  }

		  required_version = ">= 1.2.0"
}

provider "aws" {
	  region = "ap-northeast-2"
}

resource "aws_instance" "app_server" {
	  ami           = "ami-0fe95ba7739b18598"
		    instance_type = "t2.micro"
}
```
