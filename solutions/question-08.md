# Question 8 — `main.tf`

[Retour au sommaire](README.md)

```hcl
# main.tf
resource "aws_iam_group" "photographers" {
  name = local.group_full_name
  path = local.group_path
}
```
