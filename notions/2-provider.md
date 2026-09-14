# Provider AWS

Un provider permet à Terraform de communiquer avec une plateforme. Ici, le provider `aws` permettra de créer notre bucket S3.

```hcl
provider "aws" {
  region = "eu-west-3"
}
```

`aws` est le nom du provider et `region` indique la région AWS utilisée par défaut pour les resources régionales. `eu-west-3` correspond à Paris.

Le provider ne crée rien à lui seul : il fournit à Terraform les instructions nécessaires pour appeler les API AWS. Le bloc `terraform` du chapitre précédent déclare **quel plugin télécharger** ; ce bloc `provider` indique **comment l'utiliser**.

La région n'influe pas sur l'unicité d'un nom de bucket : les noms S3 doivent être uniques globalement dans une partition AWS.

---

## Authentification

Ne mettez jamais de clés AWS dans les fichiers Terraform. Configurez plutôt AWS CLI ou IAM Identity Center :

```bash
aws configure
aws sts get-caller-identity
```

Terraform réutilise alors les credentials AWS disponibles dans votre environnement.

Lors de `terraform plan` ou `terraform apply`, le provider recherche des credentials valides. Si aucun credential n'est disponible, Terraform ne peut pas lire ni créer les resources AWS et affiche une erreur d'authentification.

Pour un atelier, vérifiez toujours l'identité active avant un `apply` : cela évite de créer des resources dans le mauvais compte AWS.

---

## Suite

Nous pouvons maintenant déclarer une première resource S3.
