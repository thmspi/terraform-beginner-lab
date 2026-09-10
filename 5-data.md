# Data Block

Un bloc `data` permet de récupérer une information existante.

Contrairement à une `resource`, il ne crée pas nécessairement une nouvelle infrastructure.

---

## Exemple

Nous voulons créer des subnets dans plusieurs Availability Zones.

Nous pourrions écrire :

```text
eu-west-3a
eu-west-3b
eu-west-3c
```

directement dans notre code.

Mais nous pouvons demander à AWS quelles Availability Zones sont disponibles.

```hcl
data "aws_availability_zones" "available" {
  state = "available"
}
```

Terraform récupère alors cette information auprès d'AWS.

---

## Utiliser la data source

Nous pouvons récupérer la liste des Availability Zones avec :

```hcl
data.aws_availability_zones.available.names
```

Et la première Availability Zone avec :

```hcl
data.aws_availability_zones.available.names[0]
```

---

## Exemple avec un subnet

```hcl
resource "aws_subnet" "frontend" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.1.0/24"
  availability_zone = data.aws_availability_zones.available.names[0]
}
```

Notre subnet dépend maintenant :

* du VPC créé par Terraform ;
* d'une information récupérée depuis AWS.

---

## Resource vs Data

```text
resource
→ créer ou gérer quelque chose

data
→ récupérer quelque chose
```

Exemple :

```hcl
resource "aws_vpc" "main"
```

crée un VPC.

Alors que :

```hcl
data "aws_availability_zones" "available"
```

récupère des informations sur AWS.
