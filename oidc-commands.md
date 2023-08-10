# OIDC Commands

## RBAC

### Log in to Azure

`az login`

### Get Subscription Id

"id": "66effa16-8b4b-4047-b8e1-d390ceddd4a5"

### Validate Resource Group

`az resource list --resource-group OR-GitHub-Workshop`

resourcegroupname: OR-GitHub-Workshop

### Register a new Azure AD Application

`az ad app create --display-name gsorgithubworkshop-github-deployer -o json`

"appId": "68a1fadb-5a0d-4d48-b1db-9adbb3b9b4e8"

"id": "5aff654c-4b35-4971-bbca-d815047fab07"

### Create a Service Principal for the Application

`az ad sp create --id 68a1fadb-5a0d-4d48-b1db-9adbb3b9b4e8 -o json`

assigneeObjectId

"id": "b82b7316-01cd-4bcc-8b6e-b43f3ae60745"

tenantId

"appOwnerOrganizationId": "9637eecd-fbd4-438a-848a-4e29f4d8eae5"

### Add service principal to the Contributor role for the subscription

`az role assignment create --role contributor --subscription 66effa16-8b4b-4047-b8e1-d390ceddd4a5 --assignee-object-id  b82b7316-01cd-4bcc-8b6e-b43f3ae60745 --assignee-principal-type ServicePrincipal --scope /subscriptions/66effa16-8b4b-4047-b8e1-d390ceddd4a5`

### Grant GitHub repository access to the service principal

repo name: or-github-workshop-2023

`az rest --method POST --uri 'https://graph.microsoft.com/beta/applications/5aff654c-4b35-4971-bbca-d815047fab07/federatedIdentityCredentials' --body "{'name':'github-deploy','issuer':'https://token.actions.githubusercontent.com','subject':'repo:glensouza/or-github-workshop-2023:ref:refs/heads/main','description':'Deploy from GitHub Actions','audiences':['api://AzureADTokenExchange']}" --header "{'contenttype': 'application/json'}"`

### GitHub Secrets:

```shell
`AZURE_CLIENT_ID` `68a1fadb-5a0d-4d48-b1db-9adbb3b9b4e8`
`AZURE_TENANT_ID` `9637eecd-fbd4-438a-848a-4e29f4d8eae5`
`AZURE_SUBSCRIPTION_ID` `66effa16-8b4b-4047-b8e1-d390ceddd4a5`
```

### Action YML

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
