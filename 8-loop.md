# Boucles Terraform — count et for

Terraform permet d'éviter de dupliquer plusieurs fois la même configuration.

Nous allons progressivement transformer un seul subnet en plusieurs subnets.

---

# Étape 1 — Une seule resource

```hcl
resource "aws_subnet" "frontend" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.1.0/24"
  availability_zone = data.aws_availability_zones.available.names[0]

  tags = {
    Name = "${local.name_prefix}-frontend-subnet"
  }
}
```

Cela fonctionne.

Mais StudentHub a maintenant besoin de :

```text
frontend
backend
database
```

Nous pourrions copier cette resource trois fois.

Mais Terraform propose `count`.

---

# count

```hcl
resource "aws_subnet" "application" {
  count = 3

  vpc_id = aws_vpc.main.id

  cidr_block = cidrsubnet(
    var.vpc_cidr,
    8,
    count.index
  )

  availability_zone = data.aws_availability_zones.available.names[
    count.index % length(data.aws_availability_zones.available.names)
  ]

  tags = {
    Name = "${local.name_prefix}-subnet-${count.index}"
  }
}
```

Terraform crée :

```text
aws_subnet.application[0]
aws_subnet.application[1]
aws_subnet.application[2]
```

La variable :

```hcl
count.index
```

contient successivement :

```text
0
1
2
```

---

# Utiliser une liste

Créons une variable :

```hcl
variable "subnet_names" {
  type = list(string)

  default = [
    "frontend",
    "backend",
    "database"
  ]
}
```

Puis :

```hcl
tags = {
  Name = "${local.name_prefix}-${var.subnet_names[count.index]}-subnet"
}
```

Nous obtenons :

```text
studenthub-dev-frontend-subnet
studenthub-dev-backend-subnet
studenthub-dev-database-subnet
```

---

# Expression for

Terraform possède également des expressions `for`.

Elles permettent de transformer une collection.

Exemple :

```hcl
locals {
  subnet_names_uppercase = [
    for name in var.subnet_names :
    upper(name)
  ]
}
```

Résultat :

```text
FRONTEND
BACKEND
DATABASE
```

---

# Construire une map

Nous pouvons aller plus loin :

```hcl
locals {
  subnets = {
    for index, name in var.subnet_names :

    name => {
      cidr = cidrsubnet(var.vpc_cidr, 8, index)

      availability_zone = data.aws_availability_zones.available.names[
        index % length(data.aws_availability_zones.available.names)
      ]
    }
  }
}
```

Terraform construit quelque chose ressemblant à :

```hcl
{
  frontend = {
    cidr              = "10.0.0.0/24"
    availability_zone = "eu-west-3a"
  }

  backend = {
    cidr              = "10.0.1.0/24"
    availability_zone = "eu-west-3b"
  }

  database = {
    cidr              = "10.0.2.0/24"
    availability_zone = "eu-west-3c"
  }
}
```

---

# Bonus — for_each

Pour les ressources ayant un nom logique, `for_each` est souvent plus lisible que `count`.

```hcl
resource "aws_subnet" "application" {
  for_each = local.subnets

  vpc_id            = aws_vpc.main.id
  cidr_block        = each.value.cidr
  availability_zone = each.value.availability_zone

  tags = {
    Name = "${local.name_prefix}-${each.key}-subnet"
  }
}
```

Terraform obtient alors :

```text
aws_subnet.application["frontend"]
aws_subnet.application["backend"]
aws_subnet.application["database"]
```

---

# À retenir

```text
count
→ répéter une resource N fois

count.index
→ connaître l'index courant

for
→ transformer une collection

for_each
→ créer plusieurs resources identifiées par une clé
```
