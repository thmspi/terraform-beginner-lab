# Lab — Les groupes IAM de PhotoClub

PhotoClub organise les accès de ses équipes AWS avec des groupes IAM : `photographers`, `editors` et `administrators`. Vous allez d'abord créer un seul groupe, puis transformer votre code en module et créer les trois groupes avec une boucle.

Le lab utilise `aws_iam_group`, une resource IAM sans stockage, machine virtuelle, utilisateur, clé d'accès ni policy. Travaillez dans un compte AWS de formation et détruisez les resources à la fin.

## Objectifs

Vous allez pratiquer la configuration Terraform, le provider AWS, les types, variables, locals, data sources, opérateurs, modules, `for_each` et `count`.

## Avant de commencer

1. Vérifiez les outils et le compte AWS actif :

   ```bash
   terraform version
   aws sts get-caller-identity
   ```

2. Créez votre dossier de travail :

   ```bash
   cd lab
   mkdir photoclub
   cd photoclub
   ```

3. Après chaque étape, exécutez `terraform fmt` puis `terraform validate`.

---

## Étape 1 — Préparer Terraform

### Question 1

Créez `versions.tf` avec un bloc `terraform` qui respecte exactement ces règles :

* `required_version` accepte Terraform `1.6.0` ou plus récent ;
* le provider se nomme `aws` ;
* sa source est `hashicorp/aws` ;
* sa version accepte la série majeure `6`, mais pas `7`.

**Indice :** utilisez `">= 1.6.0"` et `"~> 6.0"`.

### Question 2

Créez `provider.tf` avec un provider AWS dont la région est `eu-west-3`.

> IAM est un service global ; cette région reste utile pour garder une configuration AWS cohérente avec les autres labs.

### Vérification

```bash
terraform init
terraform validate
```

**Résultat attendu :** le provider AWS est téléchargé et Terraform affiche `Success! The configuration is valid.`

---

## Étape 2 — Définir les entrées de PhotoClub

### Question 3

Créez `variables.tf` et ajoutez exactement ces variables :

| Nom | Type | Valeur par défaut | Description |
| --- | --- | --- | --- |
| `environment` | `string` | `"dev"` | environnement PhotoClub |
| `group_name` | `string` | `"photographers"` | nom métier du premier groupe |

Chaque variable doit avoir un `description` et un `default`.

### Question 4

Créez `terraform.tfvars` et définissez :

```hcl
environment = "dev"
```

**Question de compréhension :** quelle valeur reçoit `var.group_name` ?

**Réponse attendue :** `"photographers"`, car sa valeur par défaut est utilisée.

---

## Étape 3 — Construire le nom et le chemin du groupe

### Question 5

Créez `data.tf` et ajoutez une data source `aws_caller_identity` nommée `current`. Elle fournit l'identifiant du compte actif.

### Question 6

Créez `locals.tf` et ajoutez un local `group_full_name`. Il doit construire exactement ce format :

```text
<account-id>-<environment>-<group-name>-group
```

Utilisez `data.aws_caller_identity.current.account_id`, `var.environment` et `var.group_name`.

**Exemple attendu :** pour le compte `123456789012`, le résultat est `123456789012-dev-photographers-group`.

### Question 7

Dans le même bloc `locals`, ajoutez :

* `is_production`, de type booléen implicite, vrai uniquement si `var.environment == "prod"` ;
* `group_path`, qui vaut `"/production/"` si `is_production` est vrai et `"/training/"` sinon.

**Indice :** utilisez `==` puis `condition ? valeur_si_vrai : valeur_si_faux`.

---

## Étape 4 — Créer le premier groupe

### Question 8

Créez `main.tf` avec une resource qui respecte exactement les règles suivantes :

* type : `aws_iam_group` ;
* nom Terraform : `photographers` ;
* argument `name` : `local.group_full_name` ;
* argument `path` : `local.group_path`.

Un groupe IAM organise des utilisateurs, mais cette étape ne crée aucun utilisateur et ne donne aucun droit.

### Vérification

```bash
terraform fmt
terraform validate
terraform plan
```

**Résultat attendu :** le plan annonce uniquement `+ aws_iam_group.photographers` avec le nom et le chemin calculés.

Si le plan est correct :

```bash
terraform apply
```

---

## Étape 5 — Transformer le groupe en module

### Question 9

Créez le dossier `modules/iam-group` contenant `variables.tf`, `main.tf` et `outputs.tf`.

Dans `modules/iam-group/variables.tf`, créez exactement :

| Nom | Type | Default | Obligatoire ? |
| --- | --- | --- | --- |
| `group_name` | `string` | aucun | oui |
| `path` | `string` | `"/"` | non |

Dans `modules/iam-group/main.tf`, déplacez la resource IAM. Renommez-la `this`, utilisez `var.group_name` pour `name` et `var.path` pour `path`.

Dans `modules/iam-group/outputs.tf`, créez un output `name` avec la valeur `aws_iam_group.this.name`.

### Question 10

Supprimez la resource `aws_iam_group.photographers` à la racine. À sa place, créez un module nommé `photographers` avec :

* `source = "./modules/iam-group"` ;
* `group_name = local.group_full_name` ;
* `path = local.group_path`.

Comme vous avez déjà créé le groupe à l'étape 4, dites à Terraform que la même resource change d'adresse dans le state :

```bash
terraform state mv \
  aws_iam_group.photographers \
  module.photographers.aws_iam_group.this
```

Exécutez ensuite `terraform plan`.

**Résultat attendu :** aucun groupe ne doit être détruit ni recréé. La commande `state mv` modifie seulement l'adresse enregistrée dans le state ; elle ne modifie pas le groupe dans AWS.

---

## Étape 6 — Créer plusieurs groupes avec for_each

PhotoClub ajoute les équipes `editors` et `administrators`.

### Question 11

Ajoutez dans le `variables.tf` racine une variable `group_names` avec :

* type : `set(string)` ;
* valeur par défaut : `["photographers", "editors", "administrators"]` ;
* description : `"Noms métiers des groupes PhotoClub"`.

### Question 12

Dans `locals.tf`, créez un local `group_full_names` avec une expression `for` :

* chaque clé est un nom de `var.group_names` ;
* chaque valeur suit le format `<account-id>-<environment>-<nom>-group`.

### Question 13

Supprimez le module `photographers`. Créez un seul module nommé `group` avec :

* `for_each = local.group_full_names` ;
* `source = "./modules/iam-group"` ;
* `group_name = each.value` ;
* `path = local.group_path`.

### Vérification

Comme le groupe `photographers` existe déjà dans le module précédent, migrez son adresse avant le plan :

```bash
terraform state mv \
  module.photographers.aws_iam_group.this \
  'module.group["photographers"].aws_iam_group.this'
```

Exécutez ensuite `terraform plan`.

**Résultat attendu :** trois instances ayant des adresses proches de :

```text
module.group["photographers"]
module.group["editors"]
module.group["administrators"]
```

Appliquez seulement après avoir lu le plan.

---

## Bonus — count, sans appliquer

`count` est l'alternative indexée à `for_each`.

### Question 14

Dans une copie du dossier, sans modifier la configuration appliquée :

1. remplacez `set(string)` par `list(string)` pour `group_names` ;
2. supprimez `group_full_names` et `for_each` ;
3. ajoutez `count = length(var.group_names)` au module ;
4. construisez `group_name` avec `var.group_names[count.index]` et les mêmes préfixes ;
5. exécutez seulement `terraform plan`.

**Résultat attendu :** les adresses sont `module.group[0]`, `module.group[1]` et `module.group[2]`.

**Question de compréhension :** pourquoi préférer `for_each` ici ?

**Réponse attendue :** les noms d'équipe sont des clés métier stables et lisibles. Un index peut décaler les autres groupes lorsqu'un élément est ajouté ou supprimé dans la liste.

---

## Nettoyage obligatoire

Revenez à la dernière configuration que vous avez réellement appliquée, puis exécutez :

```bash
terraform destroy
```

Lisez le plan et confirmez uniquement si les groupes PhotoClub sont les seules resources à supprimer.
