# Commandes Terraform

Ce fichier regroupe les commandes principales utilisées pendant le workshop.

Le workflow classique est :

```text
terraform init
      ↓
terraform fmt
      ↓
terraform validate
      ↓
terraform plan
      ↓
terraform apply
      ↓
modification
      ↓
terraform plan
      ↓
terraform apply
      ↓
terraform destroy
```

---

# terraform fmt

Formate automatiquement les fichiers Terraform.

```bash
terraform fmt
```

Terraform corrige notamment :

* indentation ;
* alignement ;
* espaces ;
* présentation générale.

Pour formatter également les sous-dossiers :

```bash
terraform fmt -recursive
```

Cette commande ne modifie pas l'infrastructure AWS.

---

# terraform validate

Vérifie que la configuration Terraform est syntaxiquement et structurellement valide.

```bash
terraform validate
```

Exemple de résultat :

```text
Success! The configuration is valid.
```

Cette commande permet de détecter certaines erreurs avant d'essayer de créer l'infrastructure.

---

# terraform plan

Calcule les changements que Terraform souhaite effectuer.

```bash
terraform plan
```

Terraform compare :

```text
configuration Terraform
        +
state Terraform
        +
infrastructure
        ↓
plan
```

Le plan peut afficher :

```text
+ create
~ update
- destroy
-/+ replace
```

Exemple :

```text
Plan: 3 to add, 0 to change, 0 to destroy.
```

Cela signifie :

```text
3 resources seront créées.
```

---

## Sauvegarder un plan

```bash
terraform plan -out=tfplan
```

Puis :

```bash
terraform apply tfplan
```

Terraform appliquera exactement le plan précédemment calculé.

---

# terraform apply

Applique les changements calculés par Terraform.

```bash
terraform apply
```

Terraform affiche d'abord son plan.

Puis demande :

```text
Do you want to perform these actions?
```

Il faut répondre :

```text
yes
```

Terraform crée, modifie ou supprime ensuite les resources nécessaires.

---

# terraform destroy

Supprime les resources gérées par la configuration Terraform.

```bash
terraform destroy
```

Terraform affiche les resources qui seront supprimées avant de demander confirmation.

---

## Observer avant de détruire

Il est possible de voir ce qui serait détruit sans lancer immédiatement la suppression :

```bash
terraform plan -destroy
```

Puis :

```bash
terraform destroy
```

---

# Workflow recommandé

Pendant le workshop, utilisez systématiquement :

```bash
terraform fmt
terraform validate
terraform plan
terraform apply
```

Après une modification :

```bash
terraform fmt
terraform validate
terraform plan
```

Essayez de comprendre le plan avant d'utiliser :

```bash
terraform apply
```

Et à la fin du workshop :

```bash
terraform destroy
```

---

# Règle importante

Ne considérez pas :

```bash
terraform apply
```

comme votre première commande après une modification.

Prenez l'habitude de faire :

```text
WRITE
 ↓
FMT
 ↓
VALIDATE
 ↓
PLAN
 ↓
UNDERSTAND
 ↓
APPLY
```

Terraform est particulièrement puissant lorsque l'on comprend ce qu'il prévoit de modifier avant de lui demander de le faire.
