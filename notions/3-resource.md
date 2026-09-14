# Première resource S3

Une `resource` représente une infrastructure que Terraform doit créer ou gérer. Commençons avec un bucket S3 très simple.

```hcl
resource "aws_s3_bucket" "example" {
  bucket = "training-assets-bucket"
}
```

> Choisissez un nom réellement unique avant d'appliquer cette configuration : un bucket S3 ne peut pas partager son nom avec un bucket déjà existant.

La syntaxe générale est :

```hcl
resource "<TYPE>" "<NAME>" {
  # arguments du provider
}
```

Dans notre exemple, `aws_s3_bucket` est le type et `example` est le nom Terraform. Nous pouvons référencer le bucket avec :

```hcl
aws_s3_bucket.example.bucket
```

Le nom `example` est local à la configuration : AWS ne le voit pas. En revanche, la valeur de l'argument `bucket` devient le véritable nom visible dans AWS.

Lors d'un `terraform apply`, Terraform enregistre cette resource dans son *state* (son fichier d'état). Cela lui permet ensuite de comparer le code, l'état connu et AWS afin de savoir s'il doit créer, modifier ou supprimer un bucket.

Un bucket S3 est un conteneur pour des objets (fichiers). Cette leçon crée seulement le conteneur : elle n'importe ni ne téléverse de fichier.

La [référence des resources Terraform](https://developer.hashicorp.com/terraform/language/block/resource) décrit la syntaxe générale ; les arguments propres à `aws_s3_bucket` sont décrits dans la documentation du provider AWS.

Pour éviter les surprises, lancez d'abord `terraform plan`. Il affichera une action `+ create` avant que `terraform apply` crée réellement le bucket.

---

## Suite

Le nom est encore écrit en dur. Nous allons d'abord découvrir les types de données, puis le rendre configurable.
