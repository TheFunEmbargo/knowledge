# Pulumi

IAC for reproducable & scalable deploy environments

[Just ask the AI](https://www.pulumi.com/ai)


## Install
1. Install pulumi `curl -fsSL https://get.pulumi.com | sh`
1. Add the dependency `poetry add --group iac pulumi pulumi-azure-native`

## Usage

1. `cd ./iac`
1. `az login` (see Your Azure Account)
1. `pulumi login azblob://pulumi?storage_account=<the-azure-storage-account-that-holds-pulumis-state>`
1. `pulumi up -s prod/staging/whatever` (s stands for stack)

## Service Principal
0. Create a storage account to hold state.
1. Pulumi Service Principal will be required for service to auth.
2a. Create a service principal
    ```
    az ad sp create-for-rbac --name "pulumi-service-principal" --role Contributor
    --scopes /subscriptions/<subscription-id>
    ```
2b. Grant [Storage Blob Data Contributor](https://learn.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) access to the <the-azure-storage-account-that-holds-pulumis-state> storage account
2a. Record the Client ID, Tenant ID, and Secret in ... somewhere.
2b. Use these as [env vars](https://www.pulumi.com/registry/packages/azure-native/installation-configuration/#set-configuration-using-environment-variables):
```
export ARM_SUBSCRIPTION_ID=
export ARM_CLIENT_ID=
export ARM_CLIENT_SECRET=
export ARM_TENANT_ID=
```
3. `pulumi login azblob://pulumi?storage_account=<the-azure-storage-account-that-holds-pulumis-state>`


## Azure Functions

* [toolkit for creating functions](https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=linux%2Cisolated-process%2Cnode-v4%2Cpython-v2%2Chttp-trigger%2Ccontainer-apps&pivots=programming-language-csharp)


## How to structure a shared repo for common IAC

0. Add pulumi to the project `poetry add pulumi`
1. Create a "shared" repo with a module containing your common infrastructure & helpers. Add this submodule to your repo. 

```
git submodule add git@github.com:Company/company-shared.git company_shared
git submodule update --init --recursive
```
2. The "company_shared/company_infra" needs to create a secrets provider for future project stacks. This is common infrastructure which should be spun up in a common stack (see Common Stack section below for more) under a `common` folder. The secrets provider may be a keyvault, other examples of common infra are container registry & networks. Other generic infrastructure helpers such as `create_resource_group`, `create_key_vault` & `create_postgres_server` should also be included in shared under a `helpers` folder.
 
3. Install your shared infra as a module

```
 poetry add --group dev --editable company_shared/company_infra
```
4. Create an `infra` folder in your project

5. Create an stack in the folder, referencing your secrets provider. See **Stack**.

## Stack (Pulumi's Auth & State)

Stacks are where Pulumi stores configuration and state.

0. If this is a totally new project `pulumi new`, create your dev stack first as prompted.
1. your `Pulumi.yml` should look something like
```yml
name: ...
description: ...

runtime:
  name: python
  options:
    toolchain: poetry

backend:
  url: azblob://pulumi?storage_account=<the-azure-storage-account-that-holds-pulumis-state> // see Usage / Service Principal

config:
  pulumi:tags:
    value:
      pulumi:template: aws-python // where are you deploying to? what are you writing the pulumi in?
```
1. Change your secrets provider to the keyvault `pulumi stack change-secrets-provider "azurekeyvault://<keyvault-name>.vault.azure.net/keys/<key-name>"`.
2. your `Pulumi.dev.yml` should look something like
```
secretsprovider: azurekeyvault://<keyvault-name>.vault.azure.net/keys/<key-name>
config:
  companyInfra:commonStackName: dev
```
3.[Create another stack](https://www.pulumi.com/docs/iac/cli/commands/pulumi_stack_init/). 

## Common Stack

1. Common stack infrastructure can be made available to other stacks via `pulumi.StackReference`





