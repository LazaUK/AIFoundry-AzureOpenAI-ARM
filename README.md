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
"account_name": {
    "type": "String",
    "metadata": {
        "description": "The name of Azure OpenAI resource."
    }
}
```
*   **`accounts_location`:** The Azure region where the OpenAI resource will be created.  While `swedencentral` is the default, you can specify other supported regions.
``` JSON
"account_location": {
    "defaultValue": "swedencentral",
    "type": "String",
    "metadata": {
        "description": "The location of Azure OpenAI resource."
    }
}
```
*   **`deployment_name`:** The name of the Azure OpenAI deployment. This name, combined with the account name, will identify your deployed model.
``` JSON
"deployment_name": {
    "type": "String",
    "metadata": {
        "description": "The name of Azure OpenAI deployment."
    }
}
```

## ARM Template - Resources
The ARM template defines the following resources:
*  **`Microsoft.CognitiveServices/accounts`:** This resource creates the Azure OpenAI account.  It specifies the SKU, kind, network access controls and the location.
``` JSON
{
    "type": "Microsoft.CognitiveServices/accounts",
    "apiVersion": "2024-10-01",
    "name": "[parameters('account_name')]",
    "location": "[parameters('account_location')]",
    "sku": {
        "name": "S0"
    },
    "kind": "OpenAI",
    "properties": {
        "networkAcls": {
            "defaultAction": "Allow",
            "virtualNetworkRules": [],
            "ipRules": []
        },
        "publicNetworkAccess": "Enabled"
    }
}
```
*  **`Microsoft.CognitiveServices/accounts/deployments`:** This resource creates the actual GPT model deployment within the OpenAI account. It depends on the account resource and specifies the `gpt-35-turbo` model with version `0125`. The SKU is `GlobalBatch` with a capacity of 250.
``` JSON
{
    "type": "Microsoft.CognitiveServices/accounts/deployments",
    "apiVersion": "2024-10-01",
    "name": "[concat(parameters('account_name'), '/', parameters('deployment_name'))]",
    "dependsOn": [
        "[resourceId('Microsoft.CognitiveServices/accounts', parameters('account_name'))]"
    ],
    "sku": {
        "name": "GlobalBatch",
        "capacity": 250
    },
    "properties": {
        "model": {
            "format": "OpenAI",
            "name": "gpt-35-turbo",
            "version": "0125"
        }
    }
}
```

## Azure CLI Command

You can deploy this ARM template using the Azure CLI with the following command:

``` PowerShell
az deployment group create --resource-group <RESOURCE_GROUP_NAME> --template-file  AOAI_ARM_template.json --parameters account_name=<AZURE_OPENAI_RESOURCE_NAME> account_location=<AZURE_OPENAI_RESOURCE_LOCATION> deployment_name=<AZURE_OPENAI_DEPLOYMENT_NAME>
```
