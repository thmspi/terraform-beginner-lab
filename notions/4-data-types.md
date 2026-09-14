# Types de données Terraform

Chaque valeur Terraform possède un type. Les types les plus utiles pour notre configuration de buckets sont les suivants. La [référence officielle](https://developer.hashicorp.com/terraform/language/expressions/types) détaille tous les types et conversions.

Déclarer le type d'une variable est une bonne habitude : Terraform peut signaler immédiatement une valeur incorrecte, plutôt que de produire une erreur plus tard pendant la création d'une resource.

```hcl
variable "bucket_name" {
  type    = string
  default = "assets"
}

variable "versioning_enabled" {
  type    = bool
  default = true
}

variable "retention_days" {
  type    = number
  default = 30
}
```

```text
string → texte, par exemple "assets"
bool   → true ou false
number → nombre entier ou décimal
```

---

## Collections

```hcl
variable "bucket_names" {
  type    = set(string)
  default = ["assets", "logs", "uploads"]
}

variable "tags" {
  type = map(string)
  default = {
    ManagedBy = "Terraform"
  }
}
```

* `list(string)` est ordonnée et accessible par index ;
* `set(string)` contient des valeurs uniques, sans ordre garanti : il convient à `for_each` ;
* `map(string)` associe une clé à une valeur, par exemple des tags.

Par exemple, le premier élément d'une liste se lit avec `var.my_list[0]`, tandis qu'une valeur de map se lit avec `var.tags["ManagedBy"]`. N'essayez pas d'utiliser `[0]` sur un `set` : son ordre peut changer.

Un `object` décrit une structure fixe :

```hcl
type = object({
  name        = string
  environment = string
})
```

`null` indique l'absence de valeur. Pour un argument de resource, Terraform le traite comme si l'argument était omis.

Terraform peut effectuer certaines conversions simples, mais il est préférable de fournir directement le bon type : écrivez `true`, et non `"true"`, pour un booléen ; écrivez `30`, et non `"30"`, pour un nombre.
