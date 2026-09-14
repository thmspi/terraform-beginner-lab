# Configuration Terraform

Le bloc `terraform` configure Terraform, notamment sa version et les providers requis.

Avant de créer une resource, Terraform doit savoir deux choses : quelle version de Terraform peut lire notre code et quel plugin lui permet de communiquer avec AWS. C'est le rôle de ce bloc.

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

Dans cet exemple :

* `required_version` indique la version minimale de Terraform CLI nécessaire pour exécuter cette configuration ;
* `required_providers` liste les plugins externes dont le projet dépend ;
* `aws` est le nom court que nous utiliserons dans les blocs `provider` et `resource` ;
* `source = "hashicorp/aws"` identifie le provider AWS officiel publié dans le registre Terraform ;
* `version` limite les versions acceptables de ce provider.

Le bloc `terraform` ne configure pas encore la région AWS et ne crée aucune infrastructure. La région sera configurée dans le bloc `provider`, au chapitre suivant.

---

## Initialiser le projet

Après avoir créé ou modifié les contraintes de providers, exécutez :

```bash
terraform init
```

Cette commande prépare le dossier de travail : elle télécharge la version du provider AWS compatible avec les contraintes et initialise les fichiers internes de Terraform.

Après l'initialisation, Terraform crée généralement :

```text
.terraform/           # plugins et données de travail locales
.terraform.lock.hcl   # versions exactes sélectionnées
```

Ne modifiez pas `.terraform/` à la main et ne l'ajoutez généralement pas à Git. En revanche, conservez `.terraform.lock.hcl` dans Git : toute l'équipe utilisera alors les mêmes versions de providers.

---

## Opérateurs de version

`required_version` et `version` acceptent des contraintes de version. Elles évitent qu'une version incompatible de Terraform ou du provider soit utilisée.

```hcl
required_version = ">= 1.6.0"

version = "~> 6.0"
```

Une version est formée de nombres séparés par des points : `majeure.mineure.correctif`, par exemple `6.1.3`.

* Une version **majeure** peut introduire des changements incompatibles (`6` vers `7`).
* Une version **mineure** ajoute généralement des fonctionnalités compatibles (`6.0` vers `6.1`).
* Un **correctif** répare généralement des anomalies (`6.1.0` vers `6.1.1`).

| Contrainte | Signification |
| --- | --- |
| `= 1.6.0` | uniquement la version `1.6.0` |
| `!= 1.6.0` | toutes les versions sauf `1.6.0` |
| `>= 1.6.0` | `1.6.0` ou une version plus récente |
| `> 1.6.0` | plus récente que `1.6.0` |
| `<= 1.6.0` / `< 1.6.0` | au plus / strictement antérieure à `1.6.0` |
| `~> 6.0` | à partir de `6.0`, sans passer à `7.0` |
| `~> 6.1.0` | à partir de `6.1.0`, sans passer à `6.2.0` |

L'opérateur `~>` est souvent appelé contrainte pessimiste : il autorise les mises à jour à droite du dernier nombre indiqué, mais bloque le niveau situé immédiatement à gauche.

```text
~> 6.0   → >= 6.0.0 et < 7.0.0
~> 6.1.0 → >= 6.1.0 et < 6.2.0
```

Plusieurs contraintes sont séparées par une virgule et doivent toutes être respectées :

```hcl
version = ">= 6.0.0, < 7.0.0"
```

Dans ce projet, `~> 6.0` accepte les mises à jour compatibles du provider AWS 6.x sans adopter automatiquement une version majeure 7.x. La [référence Terraform sur les contraintes de version](https://developer.hashicorp.com/terraform/language/expressions/version-constraints) détaille ces opérateurs.

Après avoir modifié une contrainte, relancez `terraform init`. Terraform choisit une version compatible ; `terraform init -upgrade` lui demande de rechercher une version plus récente qui respecte toujours les contraintes.

---

## Suite

Le bloc suivant configurera le provider AWS. Puis nous créerons un premier bucket S3 avec un nom écrit directement dans le code.
