# Créer plusieurs buckets avec un module

Nous pouvons maintenant appeler le module plusieurs fois avec `for_each`. L'utilisateur fournit seulement des noms métiers simples.

Un seul bloc `module` suffit : Terraform le répète pour chaque élément de la collection. Cela évite de copier trois fois la même configuration.

```hcl
variable "bucket_names" {
  description = "Noms métiers des buckets à créer"
  type        = set(string)
  default     = ["assets", "logs", "uploads"]
}
```

La data source fournit le compte, et le local transforme chaque nom en nom S3 complet :

```hcl
data "aws_caller_identity" "current" {}

locals {
  bucket_full_names = {
    for name in var.bucket_names :
    name => "${data.aws_caller_identity.current.account_id}-${var.environment}-${name}-bucket"
  }
}
```

Enfin, `for_each` instancie le module une fois par nom :

```hcl
module "bucket" {
  for_each = local.bucket_full_names
  source   = "./modules/s3-bucket"

  bucket_name = each.value
  tags = {
    Environment = var.environment
    ManagedBy   = "Terraform"
  }
}
```

Avec le compte `123456789012` et l'environnement `dev`, Terraform crée :

```text
module.bucket["assets"]  → 123456789012-dev-assets-bucket
module.bucket["logs"]    → 123456789012-dev-logs-bucket
module.bucket["uploads"] → 123456789012-dev-uploads-bucket
```

`for` transforme la collection de noms en map ; `for_each` utilise cette map pour créer plusieurs instances du module, identifiées par leurs noms métiers.

Un `set` est adapté ici, car chaque nom doit être unique et l'ordre de création n'est pas important. La clé (`assets`, `logs` ou `uploads`) devient aussi une partie de l'adresse Terraform dans le state.

Attention : changer une clé de `for_each` revient généralement à supprimer l'ancienne instance et à en créer une nouvelle. Choisissez donc des noms stables et vérifiez toujours le `terraform plan` avant un `apply`.

---

## Alternative : count

`count` est une autre façon de répéter une resource ou un module. Il crée un nombre défini d'instances et les identifie par un index numérique : `0`, `1`, `2`, etc.

Pour utiliser `count`, une `list` est pratique, car elle est ordonnée :

```hcl
variable "bucket_names" {
  type    = list(string)
  default = ["assets", "logs", "uploads"]
}

module "bucket" {
  count  = length(var.bucket_names)
  source = "./modules/s3-bucket"

  bucket_name = "${data.aws_caller_identity.current.account_id}-${var.environment}-${var.bucket_names[count.index]}-bucket"
  tags = {
    Environment = var.environment
    ManagedBy   = "Terraform"
  }
}
```

Terraform attribue alors les adresses suivantes :

```text
module.bucket[0] → assets
module.bucket[1] → logs
module.bucket[2] → uploads
```

`count.index` contient l'index de l'instance en cours. Ici, il permet de lire le nom correspondant dans `var.bucket_names`.

Pour des éléments identifiés par un nom métier, préférez `for_each` : `module.bucket["logs"]` est plus explicite que `module.bucket[1]`. De plus, insérer un nom au début d'une liste avec `count` décale les index suivants et peut entraîner des remplacements inutiles. `count` est surtout adapté lorsque seules la quantité ou la position des instances comptent.
