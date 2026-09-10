# Resource Block

Une `resource` représente une infrastructure que Terraform doit créer ou gérer.

Nous allons commencer par créer le réseau de notre application StudentHub.

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "studenthub-dev-vpc"
  }
}
```

---

## Structure d'une resource

La syntaxe générale est :

```hcl
resource "<TYPE>" "<NAME>" {
}
```

Dans notre cas :

```hcl
resource "aws_vpc" "main" {
}
```

Le type est :

```text
aws_vpc
```

Le nom Terraform est :

```text
main
```

Terraform peut donc référencer cette resource avec :

```hcl
aws_vpc.main
```

---

## Réutiliser une resource

Une resource expose plusieurs attributs.

Par exemple :

```hcl
aws_vpc.main.id
```

correspond à l'identifiant AWS du VPC.

Nous pouvons utiliser cet ID pour créer un subnet :

```hcl
resource "aws_subnet" "frontend" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.0.1.0/24"
}
```

Terraform comprend automatiquement que le subnet dépend du VPC.

---

## Dépendances Terraform

Nous avons maintenant :

```text
aws_vpc.main
      ↓
aws_subnet.frontend
```

Terraform créera le VPC avant de créer le subnet.

Il n'est pas nécessaire d'écrire manuellement l'ordre des opérations.
