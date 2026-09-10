# Terraform Configuration Block

Le bloc `terraform` configure Terraform lui-même.

Il ne configure pas directement AWS.

---

## Exemple

```hcl
terraform {
  required_version = ">= 1.6.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}
```

---

## required_version

```hcl
required_version = ">= 1.6.0"
```

permet d'indiquer quelles versions de Terraform sont compatibles avec notre configuration.

Cela permet d'éviter qu'une personne exécute le projet avec une version trop ancienne.

---

## required_providers

```hcl
required_providers {
  aws = {
    source  = "hashicorp/aws"
    version = "~> 6.0"
  }
}
```

Nous indiquons ici :

```text
provider : AWS
éditeur  : HashiCorp
version  : 6.x
```

Lors de :

```bash
terraform init
```

Terraform télécharge le provider.

---

## Le fichier lock

Après `terraform init`, Terraform crée généralement :

```text
.terraform.lock.hcl
```

Ce fichier mémorise les versions utilisées.

Il doit généralement être conservé dans Git.

---

## Terraform block vs Provider block

Le bloc :

```hcl
terraform {
}
```

configure Terraform.

Le bloc :

```hcl
provider "aws" {
}
```

configure la manière dont Terraform communique avec AWS.
