# [Authentication](https://learn.hashicorp.com/tutorials/vault/getting-started-authentication?in=vault/getting-started)

### Token Authentication
* Create a new token: `$ vault token create`
```
Key                  Value
---                  -----
token                s.iyNUhq8Ov4hIAx6snw5mB2nL
token_accessor       maMfHsZfwLB6fi18Zenj3qh6
token_duration       ∞
token_renewable      false
token_policies       ["root"]
identity_policies    []
policies             ["root"]
```
This token is a child of the root token, and by default, it inherits the policies from its parent.

* Login: `$ vault login s.iyNUhq8Ov4hIAx6snw5mB2nL`

```
Success! You are now authenticated. The token information displayed below
is already stored in the token helper. You do NOT need to run "vault login"
again. Future Vault requests will automatically use this token.

Key                  Value
---                  -----
token                s.iyNUhq8Ov4hIAx6snw5mB2nL
token_accessor       maMfHsZfwLB6fi18Zenj3qh6
token_duration       ∞
token_renewable      false
token_policies       ["root"]
identity_policies    []
policies             ["root"]
```
* Revoke token: `$ vault token revoke s.iyNUhq8Ov4hIAx6snw5mB2nL`
