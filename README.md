# Azure CLI Commands Cheatsheet

[![Microsoft Azure](https://custom-icon-badges.demolab.com/badge/Microsoft%20Azure-0089D6?logo=msazure&logoColor=white)](https://azure.microsoft.com/)

This repository contains a collection of Azure CLI commands that I’ve found particularly useful as part of my learning process. I have grouped this into categories for easy reference.

---

## Contents

- [Management](#management)
- [Access & Subscriptions](#access--subscriptions)
- [Resource Inventory](#resource-inventory)
- [Resource Groups](#resource-groups)
- [Entra ID](#entra-id)
- [Roles & RBAC](#roles--rbac)
- [Virtual Machines](#virtual-machines)
- [Networking](#networking)
- [Storage](#storage)
- [Key Vault](#keyvault)
- [Azure Container Registry](#azure-container-registry-acr)
- [App Services](#app-services)
- [Azure Kubernetes Service (AKS)](#azure-kubernetes-service-aks)
- [Monitoring](#monitoring)

---

## Management

```bash
# Azure CLI version
az version

# Upgrade Azure CLI
az upgrade

# Clear CLI cache
az cache purge
```

[⬆ ʀᴇᴛᴜʀɴ ᴛᴏ ᴄᴏɴᴛᴇɴᴛꜱ](#contents)

---

## Access & Subscriptions

```bash
# Login (tenant)
az login --tenant <tenant>

# Login using device code
az login --use-device-code --tenant <tenant>

# Logout
az logout

# List subscriptions
az account list --output table

# Set active subscription
az account set --subscription <subscription>

# Show current subscription
az account show

# Show current subscription with custom columns
az account show \
  --query "{subscription:name, state:state, default:isDefault}" \
  --output table

# Set default resource group
az configure --defaults group=<resource_group>

# Set default location
az configure --defaults location=<location>
```

[⬆ ʀᴇᴛᴜʀɴ ᴛᴏ ᴄᴏɴᴛᴇɴᴛꜱ](#contents)

---

## Resource Inventory

```bash
# List all deployed resources in the current subscription
az resource list --output table

# List all deployed resources in a specific resource group
az resource list --resource-group <resource_group> --output table

# List only the resource names and types
az resource list --query "[].{Name:name, Type:type}" --output table

# List only the resource types currently deployed
az resource list --query "[].type" --output table

# List resources of a specific type
az resource list --resource-type Microsoft.Web/sites --output table

# List resources in a specific location
az resource list --location uksouth --output table

# List resource names, types, groups and locations in Arkham (read-only).
az resource list \
  --subscription <subscription> \
  --query "[].{resourceGroup:resourceGroup,name:name,type:type,location:location}" \
  --output table

# Show a specific resource
az resource show \
  --name <resource_name> \
  --resource-group <resource_group> \
  --resource-type <resource_type> \
  --output jsonc

# Delete a specific resource
az resource delete \
  --name <resource_name> \
  --resource-group <resource_group> \
  --resource-type <resource_type>
```

---

## Resource Groups

```bash
# Create resource group
az group create --name <resource_group> --location <location>

# List resource groups
az group list --output table

# List resource groups with custom columns
az group list \
  --subscription <subscription> \
  --query "[].{name:name, location:location}" \
  --output table

# Delete resource group
az group delete --name <resource_group> --yes --no-wait
```

[⬆ ʀᴇᴛᴜʀɴ ᴛᴏ ᴄᴏɴᴛᴇɴᴛꜱ](#contents)

---

## Entra ID

```bash
# List users
az ad user list --output table

# Show user
az ad user show --id <user_principal_name>

# Show user (formatted)
az ad user show --id <user_principal_name> --output jsonc

# Select specific fields
az ad user show --id <user_principal_name> \
  --query "{Name:displayName, Email:mail, UPN:userPrincipalName}" \
  --output jsonc

# List groups
az ad group list --output table

# Show group
az ad group show --group <group_name> --output jsonc
```

[⬆ ʀᴇᴛᴜʀɴ ᴛᴏ ᴄᴏɴᴛᴇɴᴛꜱ](#contents)

---

## Roles & RBAC

```bash
# List custom roles
az role definition list --custom-role-only --name <custom_role_name>

# List role assignments
az role assignment list --output table

# Assign role
az role assignment create \
  --assignee <user_or_app_id> \
  --role "Contributor" \
  --scope /subscriptions/<subscription_id>

# Remove role
az role assignment delete \
  --assignee <user_or_app_id> \
  --role "Contributor"
```

[⬆ ʀᴇᴛᴜʀɴ ᴛᴏ ᴄᴏɴᴛᴇɴᴛꜱ](#contents)

---

## Virtual Machines

```bash
# List VMs
az vm list --output table

# Create VM
az vm create \
  --resource-group <resource_group> \
  --name <vm_name> \
  --image Ubuntu2204 \
  --admin-username azureuser \
  --generate-ssh-keys

# Start VM
az vm start --name <vm_name> --resource-group <resource_group>

# Stop VM
az vm stop --name <vm_name> --resource-group <resource_group>

# Deallocate VM (stop billing)
az vm deallocate --name <vm_name> --resource-group <resource_group>

# Get VM public IP
az vm list-ip-addresses --name <vm_name> --output table
```

[⬆ ʀᴇᴛᴜʀɴ ᴛᴏ ᴄᴏɴᴛᴇɴᴛꜱ](#contents)

---

## Networking

```bash
# List VNets
az network vnet list --output table

# Create VNet
az network vnet create \
  --resource-group <resource_group> \
  --name <vnet_name> \
  --address-prefix 10.0.0.0/16 \
  --subnet-name default \
  --subnet-prefix 10.0.1.0/24

# List NSGs
az network nsg list --output table

# Create NSG rule
az network nsg rule create \
  --resource-group <resource_group> \
  --nsg-name <nsg_name> \
  --name AllowSSH \
  --protocol Tcp \
  --priority 1000 \
  --destination-port-range 22 \
  --access Allow
```

[⬆ ʀᴇᴛᴜʀɴ ᴛᴏ ᴄᴏɴᴛᴇɴᴛꜱ](#contents)

---

## Storage

```bash
# List storage accounts
az storage account list --output table

# Create storage account
az storage account create \
  --name <storage_account_name> \
  --resource-group <resource_group> \
  --location <location> \
  --sku Standard_LRS

# List containers
az storage container list \
  --account-name <storage_account_name> \
  --output table

# Upload blob
az storage blob upload \
  --account-name <storage_account_name> \
  --container-name <container> \
  --name <blob_name> \
  --file <file_path>
```

[⬆ ʀᴇᴛᴜʀɴ ᴛᴏ ᴄᴏɴᴛᴇɴᴛꜱ](#contents)

---

## Keyvault

```bash
# List Key Vaults
az keyvault list --output table

# Create Key Vault
az keyvault create \
  --name <kv_name> \
  --resource-group <resource_group> \
  --location <location>

# Set secret
az keyvault secret set \
  --vault-name <kv_name> \
  --name <secret_name> \
  --value <secret_value>

# Get secret
az keyvault secret show \
  --vault-name <kv_name> \
  --name <secret_name>
```

[⬆ ʀᴇᴛᴜʀɴ ᴛᴏ ᴄᴏɴᴛᴇɴᴛꜱ](#contents)

---

## Azure Container Registry (ACR)

```bash
# List registries
az acr list --output table

# Create ACR
az acr create \
  --name <acr_name> \
  --resource-group <resource_group> \
  --sku Basic

# Login to ACR
az acr login --name <acr_name>

# List repositories
az acr repository list --name <acr_name> --output table
```

[⬆ ʀᴇᴛᴜʀɴ ᴛᴏ ᴄᴏɴᴛᴇɴᴛꜱ](#contents)

---

## App Services

```bash
# List App Services
az webapp list --output table

# Create App Service plan
az appservice plan create \
  --name <plan_name> \
  --resource-group <resource_group> \
  --sku B1

# Create Web App
az webapp create \
  --name <app_name> \
  --resource-group <resource_group> \
  --plan <plan_name> \
  --runtime "NODE|18-lts"

# Restart Web App
az webapp restart \
  --name <app_name> \
  --resource-group <resource_group>
```

[⬆ ʀᴇᴛᴜʀɴ ᴛᴏ ᴄᴏɴᴛᴇɴᴛꜱ](#contents)

---

## Azure Kubernetes Service (AKS)

```bash
# Install kubectl via Azure CLI
az aks install-cli

# List AKS clusters
az aks list --output table

# Create AKS cluster
az aks create \
  --resource-group <resource_group> \
  --name <aks_name> \
  --node-count 2 \
  --enable-addons monitoring \
  --generate-ssh-keys

# Get cluster credentials
az aks get-credentials \
  --resource-group <resource_group> \
  --name <aks_name>

# Get credentials (overwrite existing)
az aks get-credentials \
  --resource-group <resource_group> \
  --name <aks_name> \
  --overwrite-existing

# Scale node pool
az aks scale \
  --resource-group <resource_group> \
  --name <aks_name> \
  --node-count 3

# List node pools
az aks nodepool list \
  --resource-group <resource_group> \
  --cluster-name <aks_name> \
  --output table

# Upgrade AKS cluster
az aks upgrade \
  --resource-group <resource_group> \
  --name <aks_name> \
  --kubernetes-version <version>
```

[⬆ ʀᴇᴛᴜʀɴ ᴛᴏ ᴄᴏɴᴛᴇɴᴛꜱ](#contents)

---

## Monitoring

```bash
# List Log Analytics workspaces
az monitor log-analytics workspace list --output table

# Query logs
az monitor log-analytics query \
  --workspace <workspace_id> \
  --analytics-query "AzureActivity | take 10"

# List metrics
az monitor metrics list \
  --resource <resource_id> \
  --metric "Percentage CPU"
```

[⬆ ʀᴇᴛᴜʀɴ ᴛᴏ ᴄᴏɴᴛᴇɴᴛꜱ](#contents)
