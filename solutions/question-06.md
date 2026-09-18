# Question 6 — `locals.tf`

[Retour au sommaire](README.md)

```hcl
# locals.tf
locals {
  group_full_name = "${data.aws_caller_identity.current.account_id}-${var.environment}-${var.group_name}-group"
}
```
