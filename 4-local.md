# Local Variable Block

Les `locals` permettent de calculer ou centraliser des valeurs utilisées dans notre configuration.

Contrairement aux input variables, un local n'est pas fourni par l'utilisateur.

Il est calculé dans Terraform.

---

## Notre problème

Nous voulons respecter la convention de nommage :

```text
<project>-<environment>-<resource>
```

Par exemple :

```text
studenthub-dev-vpc
studenthub-dev-frontend-subnet
studenthub-dev-backend-subnet
```

Au lieu de répéter :

```hcl
"${var.project_name}-${var.environment}"
```

partout, nous pouvons créer un local.

```hcl
locals {
  name_prefix = "${var.project_name}-${var.environment}"
}
```

Puis :

```hcl
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr

  tags = {
    Name = "${local.name_prefix}-vpc"
  }
}
```

---

## Référence

Une input variable :

```hcl
var.project_name
```

Un local :

```hcl
local.name_prefix
```

---

## Centraliser les tags

Les locals sont aussi très utiles pour éviter les répétitions.

```hcl
locals {
  name_prefix = "${var.project_name}-${var.environment}"

  common_tags = {
    Project     = var.project_name
    Environment = var.environment
    ManagedBy   = "Terraform"
  }
}
```

Puis :

```hcl
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr

  tags = merge(
    local.common_tags,
    {
      Name = "${local.name_prefix}-vpc"
    }
  )
}
```

---

## À retenir

Une manière simple de retenir la différence :

```text
variable = valeur fournie à Terraform
local    = valeur calculée par Terraform
```
