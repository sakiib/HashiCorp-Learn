# Binding Policies

* Get the list of policies: `$ vault policy list
```
default
root
```
* Policy `config.hcl`
```HCL
path "secret/data/user" {
 capabilities = ["create", "read", "update"]
}

path "secret/data/admin" {
 capabilities = ["create", "read", "update", "delete"]
}
```

* Write Policy: `$ vault policy write <name_of_policy> <path_to_hcl_file>`
* Create a token & associate with the policy: `$ vault token create -policy=<name_of_policy>`
* Login to vault with the token: `$ vault login token='<token>'`
* Export vault token: `$ export VAULT_TOKEN=<token>`
