# Question 9 — module `modules/iam-group`

[Retour au sommaire](README.md)

```hcl
# modules/iam-group/variables.tf
variable "group_name" {
  description = "Nom complet du groupe IAM"
  type        = string
}

variable "path" {
  description = "Chemin IAM du groupe"
  type        = string
  default     = "/"
}
```

```hcl
# modules/iam-group/main.tf
resource "aws_iam_group" "this" {
  name = var.group_name
  path = var.path
}
```

```hcl
# modules/iam-group/outputs.tf
output "name" {
  value = aws_iam_group.this.name
}
```
