# [Secrets Engines](https://learn.hashicorp.com/tutorials/vault/getting-started-secrets-engines)

### `secret` Prefix
* Try using a different prefix `$ vault kv get badpath/hello`, vault will return an error in making API request.
* When a request comes to vault, it matches the initial path part using a longest prefix match & then passes the request to the corresponding secrets engine enabled at that path.
* `$ vault kv get secret/hello` here, `secret` matches the longest prefix.
* By default, vault enables a secrets engine called `kv` at the path `secret/`.


### vault secrets list
* `$ vault secrets list`
```
Path          Type         Accessor              Description
----          ----         --------              -----------
cubbyhole/    cubbyhole    cubbyhole_dcd1d5f3    per-token private secret storage
identity/     identity     identity_2a37f521     identity store
secret/       kv           kv_3f24bab9           key/value secret storage
sys/          system       system_208daed2       system endpoints used for control, policy and debugging
```

### Enable another instance of `kv` in a different path
* `$vault secrets enable -path=kvpath kv` (here kvpath is the newly enables path of my own choice)
```
Path          Type         Accessor              Description
----          ----         --------              -----------
cubbyhole/    cubbyhole    cubbyhole_dcd1d5f3    per-token private secret storage
identity/     identity     identity_2a37f521     identity store
kvpath/       kv           kv_985e94e3           n/a
secret/       kv           kv_3f24bab9           key/value secret storage
sys/          system       system_208daed2       system endpoints used for control, policy and debugging
```
### Disable custom secret engine
* `$ vault secrets disable <your-path>/`
* check list now: `$ vault secrets list`
