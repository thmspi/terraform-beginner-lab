# Question 3 — `variables.tf`

[Retour au sommaire](README.md)

```hcl
# variables.tf
variable "environment" {
  description = "Environnement PhotoClub"
  type        = string
  default     = "dev"
}

variable "group_name" {
  description = "Nom métier du premier groupe"
  type        = string
  default     = "photographers"
}
```
