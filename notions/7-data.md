# Data source AWS

Une data source lit une information existante sans créer de resource. `aws_caller_identity` récupère l'identité AWS utilisée par Terraform, y compris l'identifiant du compte.

Contrairement à `resource`, un bloc `data` ne possède pas de cycle de création. Pendant le plan, Terraform interroge AWS et utilise le résultat dans le reste de la configuration.

```hcl
data "aws_caller_identity" "current" {}
```

Nous pouvons lire l'identifiant avec :

```hcl
data.aws_caller_identity.current.account_id
```

Remplaçons le local précédent pour construire un nom prévisible et pratiquement unique :

```hcl
locals {
  bucket_full_name = "${data.aws_caller_identity.current.account_id}-${var.environment}-${var.bucket_name}-bucket"
}

resource "aws_s3_bucket" "example" {
  bucket = local.bucket_full_name
}
```

Avec `bucket_name = "assets"`, `environment = "dev"` et le compte `123456789012`, le nom devient :

```text
123456789012-dev-assets-bucket
```

Le compte est donc lu automatiquement : il ne constitue pas une variable d'entrée à maintenir.

Cette data source ne crée pas de coût AWS. Elle permet aussi de détecter rapidement un mauvais compte : comparez la valeur renvoyée avec le compte attendu avant de lancer un `apply`.

La dépendance est implicite : comme `local.bucket_full_name` lit `data.aws_caller_identity.current.account_id`, Terraform récupère d'abord l'identité avant de calculer le nom du bucket.

---

## Suite

Nous utiliserons quelques opérateurs pour exprimer des conditions et transformer des valeurs.
