# [Starting the Server](https://learn.hashicorp.com/tutorials/vault/getting-started-dev-server?in=vault/getting-started)

### Starting the server in "Dev" mode
* The "Dev" server is a built-in, pre-configured server
* Useful for local development, testing & exploration
* Not very secure
* Everything is stored in-memory
* Vault is automatically unsealed
* Can optionally set the initial root token

```Shell
$ vault server -dev
```

### Verify the server running
```Shell
$ vault status
```

### Notice the VAULT_ADDR, Unseal Key & Root Token
### Development mode should NOT be used in production installations!
