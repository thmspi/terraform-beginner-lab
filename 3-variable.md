# Input Variable Block

Les input variables permettent de rendre une configuration Terraform configurable.

Au lieu d'écrire directement :

```hcl
cidr_block = "10.0.0.0/16"
```

nous pouvons déclarer une variable.

```hcl
variable "vpc_cidr" {
  description = "CIDR du VPC"
  type        = string
  default     = "10.0.0.0/16"
}
```

Puis l'utiliser :

```hcl
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr
}
```

---

## Syntaxe

Une variable peut avoir :

```hcl
variable "environment" {
  description = "Environnement cible"
  type        = string
  default     = "dev"
}
```

Les éléments les plus courants sont :

```text
description
type
default
```

---

## Utilisation

Pour utiliser une variable :

```hcl
var.environment
```

Par exemple :

```hcl
tags = {
  Environment = var.environment
}
```

---

## terraform.tfvars

Les valeurs peuvent être placées dans :

```text
terraform.tfvars
```

Exemple :

```hcl
project_name = "studenthub"
environment  = "dev"
aws_region   = "eu-west-3"
vpc_cidr     = "10.0.0.0/16"
```

Terraform charge automatiquement ce fichier.

---

## Exemple complet

```hcl
variable "project_name" {
  type    = string
  default = "studenthub"
}

variable "environment" {
  type    = string
  default = "dev"
}

variable "vpc_cidr" {
  type    = string
  default = "10.0.0.0/16"
}
```

Puis :

```hcl
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr
}
```

Les variables correspondent aux valeurs que l'on veut pouvoir changer facilement.
