# Provider Block

Le `provider` permet à Terraform de savoir **avec quelle plateforme il doit communiquer**.

Dans notre workshop, nous allons utiliser AWS.

```hcl
provider "aws" {
  region = "eu-west-3"
}
```

Ici, Terraform utilisera le provider AWS et travaillera dans la région :

```text
eu-west-3
```

qui correspond à Paris.

---

## Pourquoi un provider ?

Terraform n'est pas limité à AWS.

Il peut également communiquer avec :

* Azure ;
* Google Cloud ;
* GitHub ;
* Kubernetes ;
* Cloudflare ;
* Datadog ;
* beaucoup d'autres plateformes.

Chaque plateforme fournit un provider Terraform.

---

## Authentification AWS

Le provider AWS doit avoir des credentials pour appeler les API AWS.

Il ne faut jamais écrire directement les credentials dans le fichier Terraform.

À éviter :

```hcl
provider "aws" {
  region     = "eu-west-3"
  access_key = "AKIA..."
  secret_key = "..."
}
```

Terraform peut automatiquement utiliser les credentials configurés avec AWS CLI.

Par exemple :

```bash
aws configure
```

ou avec AWS IAM Identity Center :

```bash
aws configure sso
```

Vous pouvez vérifier votre authentification avec :

```bash
aws sts get-caller-identity
```

---

## Rendre la région configurable

Nous pouvons également utiliser une variable :

```hcl
provider "aws" {
  region = var.aws_region
}
```

La région n'est alors plus hardcodée.

Nous découvrirons les variables dans un autre exercice.
