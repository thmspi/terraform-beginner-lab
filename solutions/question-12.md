# Question 12 — compléter `locals.tf`

[Retour au sommaire](README.md)

Conservez les locals existants et ajoutez :

```hcl
locals {
  group_full_names = {
    for name in var.group_names :
    name => "${data.aws_caller_identity.current.account_id}-${var.environment}-${name}-group"
  }
}
```

Les deux blocs `locals` sont valides. Ils peuvent aussi être fusionnés dans un seul bloc.
