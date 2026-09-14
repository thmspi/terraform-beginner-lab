# Valeurs locales

Les `locals` calculent et centralisent des valeurs. À la différence d'une variable, un local n'est pas fourni par l'utilisateur.

Un local évite de recopier une même expression à plusieurs endroits. Si la convention de nommage change, une seule modification suffit.

Pour l'instant, nous construisons le nom à partir de l'environnement et du nom métier :

```hcl
locals {
  bucket_full_name = "${var.environment}-${var.bucket_name}-bucket"
}

resource "aws_s3_bucket" "example" {
  bucket = local.bucket_full_name
}
```

La référence est `local.<nom>`, ici `local.bucket_full_name`.

Les guillemets et `${...}` construisent une chaîne de caractères à partir de valeurs Terraform. Avec `environment = "dev"` et `bucket_name = "assets"`, ce local vaut `dev-assets-bucket`.

Vous ne pouvez pas définir un local dans `terraform.tfvars` : il dépend toujours du code Terraform. Les variables sont les entrées ; les locals sont les valeurs dérivées.

---

## Suite

Un nom S3 doit être unique. Nous ajouterons l'identifiant du compte AWS au préfixe après l'avoir récupéré avec une data source.
