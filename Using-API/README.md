# [Using the HTTP APIs with Authentication](https://learn.hashicorp.com/tutorials/vault/getting-started-apis?in=vault/getting-started)

* Start a new vault instance using the `config.hcl` : `$ vault server -config=config.hcl` At this point, you can use Vault's HTTP API for all your interactions.
* Initialize vault with the API
```Shell
curl \
    --request POST \
    --data '{"secret_shares": 1, "secret_threshold": 1}' \
    http://127.0.0.1:8200/v1/sys/init | jq
```
Response:
```Shell
{
  "keys": [
    "ff27b63de46b77faabba1f4fa6ef44c948e4d6f2ea21f960d6aab0eb0f4e1391"
  ],
  "keys_base64": [
    "/ye2PeRrd/qruh9Ppu9EyUjk1vLqIflg1qqw6w9OE5E="
  ],
  "root_token": "s.Ga5jyNq6kNfRMVQk2LY1j9iu"
}
```
This response contains your initial root token. It also includes the unseal key. You can use the unseal key to unseal the Vault and use the root token perform other requests in Vault that require authentication.

* To make `curl` easier: `$ export VAULT_TOKEN="s.Ga5jyNq6kNfRMVQk2LY1j9iu"`
* Using the unseal key (not the root token) from above, you can unseal the Vault via the HTTP API.
```Shell
curl \
    --request POST \
    --data '{"key": "/ye2PeRrd/qruh9Ppu9EyUjk1vLqIflg1qqw6w9OE5E="}' \
    http://127.0.0.1:8200/v1/sys/unseal | jq

```
Response:
```Shell
{
  "type": "shamir",
  "initialized": true,
  "sealed": false,
  "t": 1,
  "n": 1,
  "progress": 0,
  "nonce": "",
  "version": "1.5.0",
  "migration": false,
  "cluster_name": "vault-cluster-1b34e68e",
  "cluster_id": "2cccf342-091a-b060-900b-04c29bb71ed4",
  "recovery_seal": false,
  "storage_type": "file"
}
```

* You can invoke the Vault API to validate the initialization status: `$ curl http://127.0.0.1:8200/v1/sys/init`
Response: `{ "initialized": true }`

* Now any of the available auth methods can be enabled and configured. We'll use the `approle` in this example: `$ vault auth enable <auth_method_type>`
* To see the cURL equivalent of the CLI command to enable AppRole auth method, use the `-output-curl-string flag`.
* Enable `approle`
```Shell
curl \
    --header "X-Vault-Token: $VAULT_TOKEN" \
    --request POST \
    --data '{"type": "approle"}' \
    http://127.0.0.1:8200/v1/sys/auth/approle
```
* use the `/sys/policies/acl` endpoint to create the same policy via Vault API.
```Shell
curl \
    --header "X-Vault-Token: $VAULT_TOKEN" \
    --request PUT \
    --data '{"policy":"# Dev servers have version 2 of KV secrets engine mounted by default, so will\n# need these paths to grant permissions:\npath \"secret/data/*\" {\n  capabilities = [\"create\", \"update\"]\n}\n\npath \"secret/data/foo\" {\n  capabilities = [\"read\"]\n}\n"}' \
    http://127.0.0.1:8200/v1/sys/policies/acl/my-policy
```

* Since my-policy expects `secret/data` path to exist, enable KV v2 secrets engine at `secret/` using API.
```Shell
curl \
    --header "X-Vault-Token: $VAULT_TOKEN" \
    --request POST \
    --data '{ "type":"kv-v2" }' \
    http://127.0.0.1:8200/v1/sys/mounts/secret

```

* The following command specifies that the tokens issued under the AppRole `my-role` should be associated with `my-policy`.
```Shell
curl \
    --header "X-Vault-Token: $VAULT_TOKEN" \
    --request POST \
    --data '{"policies": ["my-policy"]}' \
    http://127.0.0.1:8200/v1/auth/approle/role/my-role

```

* The following command fetches the RoleID of the role named `my-role`.
```Shell
curl \
    --header "X-Vault-Token: $VAULT_TOKEN" \
     http://127.0.0.1:8200/v1/auth/approle/role/my-role/role-id | jq -r ".data"

```
Response: `{"role_id": "3c301960-8a02-d776-f025-c3443d513a18"}`

* This command creates a new SecretID under the `my-role`.
```Shell
curl \
    --header "X-Vault-Token: $VAULT_TOKEN" \
    --request POST \
    http://127.0.0.1:8200/v1/auth/approle/role/my-role/secret-id | jq -r ".data"
```
Response:
```Shell
{
  "secret_id": "22d1e0d6-a70b-f91f-f918-a0ee8902666b",
  "secret_id_accessor": "726ab786-70d0-8cc4-e775-c0a75070e5e5"
}
```

* These two credentials can be supplied to the login endpoint to fetch a new Vault token.
```Shell
curl --request POST \
       --data '{"role_id": "c3ec4eab-5477-c669-fca8-6a71fdf38c23", "secret_id": "fc2710e5-9536-3f4f-666d-fd5d8379b2b9"}' \
       http://127.0.0.1:8200/v1/auth/approle/login | jq -r ".auth"
```
Response:
```Shell
{
  "client_token": "s.p5NB4dTlsPiUU94RA5IfbzXv",
  "accessor": "EQTlZwOD4yIFYWIg5YY6Xr29",
  "policies": [
    "default",
    "my-policy"
  ],
  "token_policies": [
    "default",
    "my-policy"
  ],
  "metadata": {
    "role_name": "my-role"
  },
  "lease_duration": 2764800,
  "renewable": true,
  "entity_id": "4526701d-b8fd-3c39-da93-9e17506ec894",
  "token_type": "service",
  "orphan": true
}
```

* The newly acquired token can be exported as the VAULT_TOKEN environment variable value and used to authenticate subsequent Vault requests. `$ export VAULT_TOKEN="s.p5NB4dTlsPiUU94RA5IfbzXv"`

* Create a version 1 of secret named creds with a key password and its value set to `my-long-password`.
```Shell
curl \
    --header "X-Vault-Token: $VAULT_TOKEN" \
    --request POST \
    --data '{ "data": {"password": "my-long-password"} }' \
    http://127.0.0.1:8200/v1/secret/data/creds | jq -r ".data"
```

Response:
```Shell
{
  "created_time": "2020-02-05T16:51:34.0887877Z",
  "deletion_time": "",
  "destroyed": false,
  "version": 1
}
```

* Unset or Unexport env varialbe
* `$ unset VAULT_TOKEN` (doesn't work in fish) or `set -e myvar` (works in fish)
