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

## Stack (Pulumi's Auth & State)

Stacks are where Pulumi stores configuration and state.

... learn more about secrets ... 

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
    export ARM_SUBSCRIPTION_ID=
    export ARM_CLIENT_ID=
    export ARM_CLIENT_SECRET=
    export ARM_TENANT_ID=
3. `pulumi login azblob://pulumi?storage_account=<the-azure-storage-account-that-holds-pulumis-state>`


## Azure Functions

* [toolkit for creating functions](https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=linux%2Cisolated-process%2Cnode-v4%2Cpython-v2%2Chttp-trigger%2Ccontainer-apps&pivots=programming-language-csharp)
