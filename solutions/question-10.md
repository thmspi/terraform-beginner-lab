# Question 10 — appel du module

[Retour au sommaire](README.md)

Remplacez entièrement la resource racine par ce bloc dans `main.tf` :

```hcl
module "photographers" {
  source = "./modules/iam-group"

  group_name = local.group_full_name
  path       = local.group_path
}
```

Si le groupe de la question 8 a déjà été appliqué, migrez son adresse dans le state avant le plan :

```bash
terraform state mv \
  aws_iam_group.photographers \
  module.photographers.aws_iam_group.this
```
