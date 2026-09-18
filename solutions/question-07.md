# Question 7 — compléter `locals.tf`

[Retour au sommaire](README.md)

Conservez `group_full_name` dans le même bloc `locals` et ajoutez :

```hcl
locals {
  group_full_name = "${data.aws_caller_identity.current.account_id}-${var.environment}-${var.group_name}-group"

  is_production = var.environment == "prod"
  group_path    = local.is_production ? "/production/" : "/training/"
}
```
