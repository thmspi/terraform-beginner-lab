# Variables d'entrée

Les variables d'entrée rendent le nom de notre bucket configurable. Nous conservons des entrées simples : un nom métier et un environnement.

Elles permettent de réutiliser le même code pour plusieurs contextes, par exemple `dev`, `staging` et `prod`, sans modifier les resources elles-mêmes.

```hcl
variable "bucket_name" {
  description = "Nom métier du bucket, sans préfixe"
  type        = string
  default     = "assets"
}

variable "environment" {
  description = "Environnement cible"
  type        = string
  default     = "dev"
}
```

Elles se référencent avec `var.<nom>` :

```hcl
bucket = var.bucket_name
```

Le `default` rend ces deux variables optionnelles. Si aucune valeur n'est fournie, Terraform utilise `assets` et `dev`. Sans `default`, la variable est obligatoire : Terraform demande une valeur au lancement ou échoue si aucun mécanisme d'entrée ne la fournit.

---

## Fournir une valeur

Terraform accepte notamment les valeurs depuis :

* `terraform.tfvars`, chargé automatiquement ;
* l'option `-var` ;
* une variable d'environnement `TF_VAR_<nom>`.

```hcl
# terraform.tfvars
bucket_name = "assets"
environment = "prod"
```

```bash
terraform plan -var="environment=prod"
export TF_VAR_bucket_name="assets"
```

> L'option Terraform est `-var`, et non `--variables`.

Pour un atelier, `terraform.tfvars` est souvent la solution la plus simple, car les valeurs ne doivent pas être répétées dans chaque commande. N'y placez jamais de secret et ne versionnez pas le fichier s'il contient des données sensibles.

Les méthodes d'entrée ont une priorité. En pratique, évitez d'en mélanger plusieurs pour la même variable : savoir d'où vient une valeur rend un `plan` beaucoup plus facile à comprendre.

La [documentation des variables d'entrée](https://developer.hashicorp.com/terraform/language/values/variables) précise aussi leur ordre de priorité.

---

## Suite

Nous allons calculer un nom de bucket cohérent à partir de ces entrées.
