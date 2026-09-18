# Terraform Beginner Lab

Ce dépôt propose un parcours d'initiation à Terraform : les notions essentielles, un lab pratique autour des groupes IAM AWS et les solutions détaillées de chaque question.

## Parcours recommandé

1. Consultez les [notions Terraform](#notions-terraform).
2. Réalisez le [lab PhotoClub](lab/README.md) sans regarder les solutions.
3. Comparez votre travail avec les [solutions](#solutions-du-lab).

## Notions Terraform

1. [Terraform](notions/1-terraform.md)
2. [Providers](notions/2-provider.md)
3. [Resources](notions/3-resource.md)
4. [Types de données](notions/4-data-types.md)
5. [Variables](notions/5-variable.md)
6. [Locals](notions/6-local.md)
7. [Data sources](notions/7-data.md)
8. [Opérateurs](notions/8-operators.md)
9. [Modules](notions/9-module.md)
10. [Boucles](notions/10-loop.md)
11. [Commandes Terraform](notions/11-commands.md)

## Lab pratique

Le [lab PhotoClub](lab/README.md) permet de mettre en pratique la configuration Terraform, le provider AWS, les variables, les locals, les data sources, les modules, `for_each` et `count`.

## Solutions du lab

1. [Question 1](solutions/question-01.md)
2. [Question 2](solutions/question-02.md)
3. [Question 3](solutions/question-03.md)
4. [Question 4](solutions/question-04.md)
5. [Question 5](solutions/question-05.md)
6. [Question 6](solutions/question-06.md)
7. [Question 7](solutions/question-07.md)
8. [Question 8](solutions/question-08.md)
9. [Question 9](solutions/question-09.md)
10. [Question 10](solutions/question-10.md)
11. [Question 11](solutions/question-11.md)
12. [Question 12](solutions/question-12.md)
13. [Question 13](solutions/question-13.md)
14. [Question 14](solutions/question-14.md)

## Prérequis

- Terraform 1.6 ou plus récent ;
- AWS CLI configurée ;
- un compte AWS de formation pour réaliser le lab.

Vérifiez toujours le résultat de `terraform plan` avant d'exécuter `terraform apply`, puis détruisez les ressources du lab avec `terraform destroy`.
