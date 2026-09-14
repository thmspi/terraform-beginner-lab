# Module S3 réutilisable

Notre bucket est maintenant configurable et nommé à partir du compte, de l'environnement et d'un nom métier. Regroupons cette logique dans un module réutilisable.

Un module est un dossier de fichiers Terraform. Le dossier principal du projet est le *root module* ; `modules/s3-bucket` est un *child module* appelé depuis ce projet.

```text
modules/
└── s3-bucket/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

## Variables du module

```hcl
# modules/s3-bucket/variables.tf
variable "bucket_name" {
  description = "Nom complet et unique du bucket S3"
  type        = string
}

variable "tags" {
  type    = map(string)
  default = {}
}
```

Une variable sans `default`, comme `bucket_name`, est obligatoire pour l'appelant du module. Une variable avec `default`, comme `tags`, est optionnelle.

Cela forme le contrat du module : l'appelant doit fournir un nom complet, tandis que les tags peuvent être omis. Cette distinction est importante pour qu'un module soit facile à réutiliser sans cacher les informations indispensables.

## Resource et output

```hcl
# modules/s3-bucket/main.tf
resource "aws_s3_bucket" "this" {
  bucket = var.bucket_name
  tags   = var.tags
}
```

```hcl
# modules/s3-bucket/outputs.tf
output "name" {
  value = aws_s3_bucket.this.bucket
}
```

## Appeler le module

```hcl
data "aws_caller_identity" "current" {}

locals {
  bucket_full_name = "${data.aws_caller_identity.current.account_id}-${var.environment}-${var.bucket_name}-bucket"
}

module "assets_bucket" {
  source = "./modules/s3-bucket"

  bucket_name = local.bucket_full_name
  tags = {
    Environment = var.environment
    ManagedBy   = "Terraform"
  }
}
```

L'output est accessible avec `module.assets_bucket.name`.

Le module ne choisit pas l'environnement ni l'ID de compte : le root module calcule le nom et le transmet en entrée. Cette séparation rend le module plus générique : il sait créer un bucket, quel que soit le contexte qui l'appelle.
