# [Your First Secret](https://learn.hashicorp.com/tutorials/vault/getting-started-first-secret?in=vault/getting-started)

### Writing a Secret
* Let's write a secret to Key/Value v2 secrets engine when running a dev server. 
* Use the `vault kv put <path> <key>=<value>` command.
* Example: `vault kv put secret/hello foo=world` & multiple secrets can be written as well.
* Notice that, command creates a new version of the secrets and replaces any pre-existing data at the path if any.
### Getting a Secret
* `vault kv get secret/hello`
* To print only the value of a given field: use the `-field=<key_name>` flag. E.g: `vault kv get -field=excited secret/hello`
* Optional JSON output using jq tool: `vault kv get -format=json secret/hello | jq -r .data.data.excited`
### Deleting a Secret
* `vault kv delete secret/hello`

### In addition to writing data directly from command line, it can also read values in key, value pairs from stdin & files. For real secrets files are preferred.
