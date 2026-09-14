# Opérateurs Terraform

Les opérateurs combinent ou transforment des valeurs dans les expressions Terraform. La [documentation officielle](https://developer.hashicorp.com/terraform/language/expressions/operators) est la référence complète.

Une expression produit toujours une valeur. Par exemple, `var.environment == "prod"` produit un booléen : `true` si l'environnement est `prod`, sinon `false`.

## Comparaisons et conditions

```hcl
locals {
  is_production = var.environment == "prod"
  bucket_suffix = local.is_production ? "production" : "nonprod"
}
```

* `==` et `!=` comparent deux valeurs ;
* `<`, `<=`, `>` et `>=` comparent des nombres ;
* `condition ? si_vrai : si_faux` choisit une valeur.

Les deux résultats d'une expression conditionnelle doivent être compatibles. Ici, les deux branches sont des chaînes de caractères, donc Terraform peut choisir l'une ou l'autre sans ambiguïté.

## Logique

```hcl
locals {
  enable_logging = var.environment == "prod" || var.environment == "staging"
  is_valid_name  = var.bucket_name != "" && length(var.bucket_name) <= 30
  disable_feature = !local.enable_logging
}
```

* `&&` signifie « et » ;
* `||` signifie « ou » ;
* `!` inverse un booléen.

Les parenthèses rendent les conditions plus faciles à lire et contrôlent l'ordre d'évaluation : `a && (b || c)` est plus clair que la même expression sans parenthèses.

## Arithmétique

```hcl
locals {
  copies     = 2 + 1
  shard_index = 10 % 3
}
```

Les opérateurs sont `+`, `-`, `*`, `/`, `%` et la négation unaire `-`. Utilisez des parenthèses pour rendre la priorité explicite : `(1 + 2) * 3` vaut `9`.

Pour construire des noms, préférez l'interpolation déjà vue dans les locals, par exemple `"${var.environment}-${var.bucket_name}"`, plutôt que d'essayer d'additionner des chaînes.
