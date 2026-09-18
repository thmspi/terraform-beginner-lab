# Question 14 — alternative avec `count`

[Retour au sommaire](README.md)

Réalisez cette variante uniquement dans une copie du lab. Remplacez `group_names`, supprimez `group_full_names`, puis utilisez ce module :

```hcl
variable "group_names" {
  description = "Noms métiers des groupes PhotoClub"
  type        = list(string)
  default     = ["photographers", "editors", "administrators"]
}

module "group" {
  count  = length(var.group_names)
  source = "./modules/iam-group"

  group_name = "${data.aws_caller_identity.current.account_id}-${var.environment}-${var.group_names[count.index]}-group"
  path       = local.group_path
}
```

Les adresses Terraform attendues sont `module.group[0]`, `module.group[1]` et `module.group[2]`.

`for_each` est préférable ici, car les noms d'équipe sont des clés métier stables et lisibles. Avec `count`, l'ajout ou la suppression d'un élément peut décaler les index des autres groupes.
