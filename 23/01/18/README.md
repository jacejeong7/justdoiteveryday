

[Query Data with Outputs | Terraform | HashiCorp Developer](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/aws-outputs)

- outputs.tf

```bash
output "instance_id" {
	  description = "ID of the EC2 instance"
		    value       = aws_instance.app_server.id
}

output "instance_public_ip" {
	  description = "Public IP address of the EC2 instance"
		    value       = aws_instance.app_server.public_ip
}
```

- commands

```bash
terraform apply

terraform output

```
