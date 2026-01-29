

# Azure Cloud Security CTF Command Notes

---

## Phase 0 Identity and Context

### Who am I

```bash
az account show
```

Shows

* Subscription ID
* Tenant ID
* User or service principal
* Account type

If you see user type servicePrincipal you are likely running as an app or managed identity.

---

### List all accessible subscriptions

```bash
az account list -o table
```

Set active subscription

```bash
az account set --subscription SUBSCRIPTION_ID
```

---

## Azure AD and Identity

### Who am I in Azure AD

```bash
az ad signed-in-user show
```

If this fails you may be a service principal.

---

### Check role assignments

```bash
az role assignment list --assignee YOUR_OBJECT_ID -o table
```

If object ID is unknown

```bash
az ad signed-in-user show --query id -o tsv
```

High value roles

* Owner
* Contributor
* User Access Administrator

---

### List all role assignments in subscription

```bash
az role assignment list -o table
```

Useful when enumeration is allowed.

---

## Managed Identities

### Check if resource has managed identity

```bash
az vm identity show --name VM_NAME --resource-group RG
```

For App Services

```bash
az webapp identity show --name APP --resource-group RG
```

Managed identities often have high privileges.

---

## Storage Accounts

### List storage accounts

```bash
az storage account list -o table
```

---

### List containers

```bash
az storage container list \
--account-name STORAGE_ACCOUNT \
--auth-mode login
```

---

### List blobs

```bash
az storage blob list \
--container-name CONTAINER \
--account-name STORAGE_ACCOUNT \
--auth-mode login
```

---

### Download blob

```bash
az storage blob download \
--container-name CONTAINER \
--name flag.txt \
--file flag.txt \
--account-name STORAGE_ACCOUNT \
--auth-mode login
```

---

### Check public access

```bash
az storage account show \
--name STORAGE_ACCOUNT \
--query allowBlobPublicAccess
```

---

### SAS tokens

If you find SAS tokens in code or URLs you can use them directly.

---

## Azure Key Vault

### List key vaults

```bash
az keyvault list -o table
```

---

### List secrets

```bash
az keyvault secret list \
--vault-name VAULT
```

---

### Read secret

```bash
az keyvault secret show \
--vault-name VAULT \
--name SECRET_NAME
```

Key Vaults often store credentials or connection strings.

---

## Virtual Machines

### List VMs

```bash
az vm list -o table
```

---

### Get VM details

```bash
az vm show \
--name VM \
--resource-group RG
```

---

### Run command on VM

```bash
az vm run-command invoke \
--command-id RunShellScript \
--name VM \
--resource-group RG \
--scripts "whoami"
```

If allowed this is instant shell level access.

---

## App Services and Functions

### List web apps

```bash
az webapp list -o table
```

---

### App settings and environment variables

```bash
az webapp config appsettings list \
--name APP \
--resource-group RG
```

Often contains secrets.

---

### Azure Functions

```bash
az functionapp list -o table
```

---

### Function app settings

```bash
az functionapp config appsettings list \
--name FUNC \
--resource-group RG
```

---

## Logic Apps

### List Logic Apps

```bash
az logicapp list -o table
```

---

### Show workflow

```bash
az logicapp show \
--name APP \
--resource-group RG
```

Look for callback URLs and connectors.

---

## Networking

### List network security groups

```bash
az network nsg list -o table
```

---

### View rules

```bash
az network nsg rule list \
--nsg-name NSG \
--resource-group RG
```

Check for 0.0.0.0/0 inbound rules.

---

## Azure Resource Manager Enumeration

### List all resources

```bash
az resource list -o table
```

This often reveals hidden services.

---

## Logging and Monitoring

### Log Analytics workspaces

```bash
az monitor log-analytics workspace list -o table
```

---

### Diagnostic settings

```bash
az monitor diagnostic-settings list \
--resource RESOURCE_ID
```

Logs sometimes contain secrets.

---

## Enumeration When Azure AD is Restricted

If Azure AD enumeration is blocked you should test capabilities directly.

Example

```bash
az storage account list
az keyvault list
az webapp list
az vm list
```

If a command works you have permission even if you cannot list roles.

---

## Common Azure CTF Escalation Paths

* App Service settings reveal credentials
* Managed identity has Contributor or Owner role
* Key Vault secret reveals service principal secret
* Logic App callback URL allows unauthorized access
* Storage account public blob contains config files

---

## Interview Quality Explanation

When asked what you are doing
I first identify my identity and subscription then enumerate role assignments. If identity permissions are restricted I pivot to service level probing to infer effective permissions and look for secrets in Key Vaults storage accounts or app configurations.

---

## Fast Azure CTF Flow

```text
az account show
az role assignment list
az resource list
az storage account list
az keyvault list
az webapp config appsettings list
```

---

If you want next I can give

* GCP command notes in the same format
* Azure privilege escalation playbook
* Mock Azure CTF scenario walkthrough
* One page Azure cheat sheet for interviews

Just tell me what you want.
