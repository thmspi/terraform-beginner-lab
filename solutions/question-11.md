# Question 11 — compléter `variables.tf`

[Retour au sommaire](README.md)

Conservez les deux variables existantes et ajoutez :

```hcl
variable "group_names" {
  description = "Noms métiers des groupes PhotoClub"
  type        = set(string)
  default     = ["photographers", "editors", "administrators"]
}
```
