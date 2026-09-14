# Commandes Terraform

Le workflow du parcours est :

```text
terraform init → terraform fmt → terraform validate → terraform plan → terraform apply
```

Chaque commande a un objectif différent : ne sautez pas directement à `apply`. Les quatre premières permettent de détecter un problème avant toute modification du compte AWS.

```bash
# Télécharge les providers et initialise le dossier.
terraform init

# Formate les fichiers .tf.
terraform fmt

# Vérifie la syntaxe et la cohérence de la configuration.
terraform validate

# Affiche les changements prévus, sans les appliquer.
terraform plan

# Applique après confirmation interactive.
terraform apply
```

`fmt` modifie uniquement la mise en forme des fichiers `.tf`. `validate` vérifie que la configuration est cohérente, mais n'interroge pas forcément AWS. `plan` compare la configuration, le state et AWS pour afficher les changements prévus ; il ne crée rien.

Lisez particulièrement les symboles du plan : `+` crée, `~` modifie, `-` supprime et `-/+` remplace une resource.

## Appliquer automatiquement

Pour les exercices ou l'automatisation, `-auto-approve` supprime la demande de confirmation :

```bash
terraform apply -auto-approve
```

Utilisez cette option seulement après `terraform validate` et après avoir compris le résultat de `terraform plan`.

## Nettoyer l'exercice

```bash
terraform destroy
```

`destroy` supprime les buckets gérés par la configuration après confirmation. Vérifiez toujours le plan avant de confirmer.

À la fin d'un exercice, exécutez `terraform destroy` pour ne pas conserver de resources inutiles. Ne lancez jamais cette commande dans un compte ou un environnement dont vous ne comprenez pas le state.
