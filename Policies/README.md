# [Policies](https://learn.hashicorp.com/tutorials/vault/getting-started-policies?in=vault/getting-started)

### Policy Format
* Policies are authored in HCL, but are JSON compatible. Here is an example policy:
```HCL
# Dev servers have version 2 of KV secrets engine mounted by default, so will
# need these paths to grant permissions:
path "secret/data/*" {
  capabilities = ["create", "update"]
}

path "secret/data/foo" {
  capabilities = ["read"]
}
```
* With this policy, a user could write any secret to secret/data/, except to secret/data/foo, where only read access is allowed. Policies default to deny, so any access to an unspecified path is not allowed.
* Built-in policies `root` & `default` are required & can't be deleted.
* View the default policy: `$ vault policy read default`
* Write policy help: `$ vault policy write -h`
