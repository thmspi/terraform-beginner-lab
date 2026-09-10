# Terraform Module

Un module permet de regrouper plusieurs éléments Terraform dans un composant réutilisable.

Un module peut contenir :

* resources ;
* variables ;
* locals ;
* data sources ;
* outputs.

---

## Pourquoi utiliser un module ?

Imaginons que StudentHub ait besoin de plusieurs Security Groups.

Nous pourrions copier le même code plusieurs fois.

Mais nous pouvons aussi créer un module.

Structure :

```text
modules/
└── security-group/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

---

## Variables du module

Dans :

```text
modules/security-group/variables.tf
```

```hcl
variable "name" {
  type = string
}

variable "vpc_id" {
  type = string
}

variable "tags" {
  type    = map(string)
  default = {}
}
```

---

## Resource du module

Dans :

```text
modules/security-group/main.tf
```

```hcl
resource "aws_security_group" "this" {
  name        = var.name
  description = "Managed by Terraform"
  vpc_id      = var.vpc_id

  tags = merge(
    var.tags,
    {
      Name = var.name
    }
  )
}
```

---

## Output

Dans :

```text
modules/security-group/outputs.tf
```

```hcl
output "id" {
  value = aws_security_group.this.id
}
```

---

## Utiliser le module

Dans notre projet principal :

```hcl
module "application_security_group" {
  source = "./modules/security-group"

  name   = "${local.name_prefix}-application-sg"
  vpc_id = aws_vpc.main.id
  tags   = local.common_tags
}
```

---

## Récupérer un output du module

```hcl
module.application_security_group.id
```

---

## Modèle mental

```text
INPUTS
  ↓
MODULE
  ↓
RESOURCES
  ↓
OUTPUTS
```

Un module permet de créer une abstraction réutilisable au-dessus de plusieurs ressources Terraform.
