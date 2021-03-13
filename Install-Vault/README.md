# [Install Vault](https://learn.hashicorp.com/tutorials/vault/getting-started-install?in=vault/getting-started)

## download vault, unzip it & run the commands below.

```Shell
$ curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
$ sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
$ sudo apt-get update && sudo apt-get install vault
```
