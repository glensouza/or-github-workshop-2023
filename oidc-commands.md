# OIDC Commands

These commands were taken from [Microsoft Learn](https://learn.microsoft.com/en-us/azure/developer/github/connect-from-azure?tabs=azure-cli,linux&WT.mc_id=javascript-70241-aapowell).

## RBAC

### Log in to Azure

`az login`

### Get Subscription Id

"id": "xxx-xxx-xxx"

### Validate Resource Group

`az resource list --resource-group OR-GitHub-Workshop`

resourcegroupname: OR-GitHub-Workshop

### Register a new Azure AD Application

`az ad app create --display-name gsorgithubworkshop-github-deployer -o json`

"appId": "xxxx-xxx-xxxx"

"id": "xxxxxxxx-xxxxxxx-xxxxxxxx"

### Create a Service Principal for the Application

`az ad sp create --id xxxx-xxx-xxxx -o json`

assigneeObjectId

"id": "xxxx-xxxxx-xxxx"

tenantId

"appOwnerOrganizationId": "xxxxxx-xxxxx-xxxxxx"

### Add service principal to the Contributor role for the subscription

`az role assignment create --role contributor --subscription xxx-xxx-xxx --assignee-object-id  xxxx-xxxxx-xxxx --assignee-principal-type ServicePrincipal --scope /subscriptions/xxx-xxx-xxx`

### Grant GitHub repository access to the service principal and federated credentials

repo name: or-github-workshop-2023

`az rest --method POST --uri 'https://graph.microsoft.com/beta/applications/xxxxxxxx-xxxxxxx-xxxxxxxx/federatedIdentityCredentials' --body "{'name':'github-deploy','issuer':'https://token.actions.githubusercontent.com','subject':'repo:glensouza/or-github-workshop-2023:ref:refs/heads/main','description':'Deploy from GitHub Actions','audiences':['api://AzureADTokenExchange']}" --header "{'contenttype': 'application/json'}"`

## GitHub Secrets

```shell
`AZURE_CLIENT_ID` `xxxx-xxx-xxxx`
`AZURE_TENANT_ID` `xxxxxx-xxxxx-xxxxxx`
`AZURE_SUBSCRIPTION_ID` `xxx-xxx-xxx`
```

## Action YML

```yml
actions yml:

permissions:
      id-token: write
      contents: read

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Az CLI login'
      uses: azure/login@v1
      with:
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
```
