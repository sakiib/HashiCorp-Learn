# [Dynamic Secrets](https://learn.hashicorp.com/tutorials/vault/getting-started-dynamic-secrets?in=vault/getting-started)

### [Google Cloud Secrets Engine] (https://www.vaultproject.io/docs/secrets/gcp)

* Enable the Google Cloud secrets engine: `$ vault secrets enable gcp`
* Configure the secrets engine with account credentials, or leave blank or unwritten to use Application Default Credentials.
  `$ vault write gcp/config credentials=@path_to_json_file` - download from google cloud appscode-testing.
  
* To configure a roleset that generates OAuth2 access tokens (preferred):
```Shell
$ vault write gcp/roleset/my-token-roleset \
                              project="appscode-testing" \
                              secret_type="access_token"  \
                              token_scopes="https://www.googleapis.com/auth/cloud-platform" \
                              bindings='resource "//cloudresourcemanager.googleapis.com/projects/appscode-testing" {
                                  roles = ["roles/viewer"]
                                }'
```
                              
* Access Tokens: `$ vault read gcp/token/my-token-roleset`
