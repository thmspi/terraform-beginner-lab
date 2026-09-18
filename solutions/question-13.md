# Question 13 — boucle avec `for_each`

[Retour au sommaire](README.md)

Remplacez le module `photographers` par ce bloc dans `main.tf` :

```hcl
module "group" {
  for_each = local.group_full_names
  source   = "./modules/iam-group"

  group_name = each.value
  path       = local.group_path
}
```

Si le module de la question 10 a déjà été appliqué, migrez d'abord son groupe existant :

```bash
terraform state mv \
  module.photographers.aws_iam_group.this \
  'module.group["photographers"].aws_iam_group.this'
```
