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
