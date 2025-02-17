# Azure AI Foundry - GPT model deployment with ARM template

This repository provides an ARM template for automating the deployment of GPT models on Azure AI Foundry.

[Azure Resource Manager (ARM)](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/overview) templates are a declarative way to define your infrastructure as code. This allows you to consistently and repeatedly deploy resources, simplifying infrastructure management and enabling Infrastructure as Code (IaC) practices.  Using an ARM template for GPT model deployment ensures consistent configuration, reduces manual steps and makes it easier to manage and replicate your AI environment.

> [!NOTE]
> Provided ARM template utilises API version **2024-10-01**, described [here](https://learn.microsoft.com/en-us/azure/templates/microsoft.cognitiveservices/2024-10-01/accounts?pivots=deployment-language-arm-template).

## Table of contents:
- [ARM Template - Parameters](#arm-template---parameters)
- [ARM Template - Resources](#arm-template---resources)
- [Azure CLI Command](#azure-cli-command)

## ARM Template - Parameters

This template defines the following parameters that you need to provide when deploying it:

*   **`accounts_name`:** The name of the Azure OpenAI resource. This should be a unique name within your Azure subscription.
``` JSON
"accounts_name": {
    "type": "String",
    "metadata": {
        "description": "The name of Azure OpenAI resource."
    }
}
```
*   **`accounts_location`:** The Azure region where the OpenAI resource will be created.  While `eastus` is the default, you can specify other supported regions.
*   **`deployment_name`:** The name of the Azure OpenAI deployment. This name, combined with the account name, will identify your deployed model.

## ARM Template - Resources

The ARM template defines the following resources:

1.  **`Microsoft.CognitiveServices/accounts` (OpenAI Account):** This resource creates the Azure OpenAI account.  It specifies the SKU (`S0`), kind (`OpenAI`), network access controls (allowing all public network access by default), and the location.

2.  **`Microsoft.CognitiveServices/accounts/deployments` (GPT Model Deployment):** This resource creates the actual GPT model deployment within the OpenAI account. It depends on the account resource and specifies the `gpt-35-turbo` model with version `0125`. The SKU is `GlobalBatch` with a capacity of 250. This configuration is suitable for batch processing and inference.

## Azure CLI Command

You can deploy this ARM template using the Azure CLI with the following command:

```azurecli
az deployment group create \
  --resource-group <your_resource_group_name> \
  --template-file <path_to_your_template.json> \
  --parameters accounts_name=<your_openai_account_name> accounts_location=<your_openai_account_location> deployment_name=<your_deployment_name>
