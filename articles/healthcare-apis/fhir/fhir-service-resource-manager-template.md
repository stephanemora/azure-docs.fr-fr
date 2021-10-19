---
title: Déployer le service FHIR des API de santé Azure à l’aide d’un modèle ARM
description: Découvrez comment déployer le service FHIR à l’aide d’un modèle de Azure Resource Manager (modèle ARM)
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.author: zxue
ms.date: 08/06/2021
ms.openlocfilehash: 97322c917e12e451affc2fe8a67bb8c8f6200a74
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782419"
---
# <a name="deploy-a-fhir-service-within-azure-healthcare-apis---using-arm-template"></a>Déployer un service FHIR dans les API de santé Azure-à l’aide d’un modèle ARM

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Dans cet article, vous allez apprendre à déployer le service FHIR dans les API de santé Azure (par le biais du service FHIR) à l’aide du modèle de Azure Resource Manager (modèle ARM). Nous vous proposons deux options, à l’aide de PowerShell ou de l’interface CLI.

Un [modèle ARM](../../azure-resource-manager/templates/overview.md) est un fichier JSON qui définit l’infrastructure et la configuration de votre projet. Le modèle utilise la syntaxe déclarative. Dans la syntaxe déclarative, vous décrivez le déploiement souhaité sans écrire la séquence de commandes de programmation pour créer le déploiement.

## <a name="prerequisites"></a>Prérequis

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/).
* Si vous voulez exécuter le code localement :
    * [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

* Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/).
* Si vous voulez exécuter le code localement :
    * Un shell Bash (comme Git Bash, qui est inclus dans [Git pour Windows](https://gitforwindows.org)).
    * [Azure CLI](/cli/azure/install-azure-cli).

---

## <a name="review-the-arm-template"></a>Passer en revue le modèle ARM

Le modèle utilisé dans cet article provient des [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/azure-api-for-fhir/).

Le modèle définit trois ressources Azure :
- Microsoft. HealthcareApis/espaces de travail
- Microsoft.HealthcareApis/workspaces/fhirservices      
- Microsoft.Storage/storageAccounts

Vous pouvez déployer la ressource de service FHIR en **supprimant** la ressource espaces de travail, la ressource de stockage et la `dependsOn` propriété dans la ressource « Microsoft. HealthcareApis/Workspaces/fhirservices ».


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "authorityurl": {
            "type": "string",
            "defaultValue": "https://login.microsoftonline.com"
        },
        "tagName": {
            "type": "string",
            "defaultValue": "My Deployment"
        },
        "region": {
            "type": "string",
                  "allowedValues": [
                "australiaeast",
                "canadacentral",
                "eastus",
                "eastus2",
                "germanywestcentral",
                "japaneast",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "switzerlandnorth",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus2"
            ]
        },
        "workspaceName": {
            "type": "string"
        },
        "fhirServiceName": {
            "type": "string"
        },
        "tenantid": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountConfirm": {
            "type": "bool",
            "defaultValue": true
        },
        "AccessPolicies": {
            "type": "array",
            "defaultValue": []
        },
        "smartProxyEnabled": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "variables": { 
        "authority": "[Concat(parameters('authorityurl'), '/', parameters('tenantid'))]",
        "createManagedIdentity": true,
        "managedIdentityType": {
            "type": "SystemAssigned"
        },
        "storageBlobDataContributerRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'ba92f5b4-2d11-453d-a403-e96b0029c9fe')]"
    },
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2020-11-01-preview",
            "location": "[parameters('region')]",
            "properties": {}
        },
       {          
            "type": "Microsoft.HealthcareApis/workspaces/fhirservices",
            "kind": "fhir-R4",
            "name": "[concat(parameters('workspaceName'), '/', parameters('fhirServiceName'))]",
            "apiVersion": "2020-11-01-preview",
            "location": "[parameters('region')]",
            "dependsOn": [
                "[resourceId('Microsoft.HealthcareApis/workspaces', parameters('workspaceName'))]"
            ],
            "tags": {
                "environmentName": "[parameters('tagName')]"
            },
            "properties": {
                "accessPolicies": "[parameters('AccessPolicies')]",
                "authenticationConfiguration": {
                    "authority": "[variables('Authority')]",
                    "audience": "[concat('https//', parameters('workspaceName'), '-', parameters('fhirServiceName'), '.fhir.azurehealthcareapis.com')]",
                    "smartProxyEnabled": "[parameters('smartProxyEnabled')]"
                },
                "corsConfiguration": {
                    "allowCredentials": false,
                    "headers": ["*"],
                    "maxAge": 1440,
                    "methods": ["DELETE", "GET", "OPTIONS", "PATCH", "POST", "PUT"],
                    "origins": ["https://localhost:6001"]
                },
                "exportConfiguration": {
                    "storageAccountName": "[parameters('storageAccountName')]"
                }
            },
            "identity": "[if(variables('createManagedIdentity'), variables('managedIdentityType'), json('null'))]"
        },
        {
            "name": "[parameters('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "supportsHttpsTrafficOnly": "true"
            },
            "condition": "[parameters('storageAccountConfirm')]",
            "dependsOn": [
                "[parameters('fhirServiceName')]"
            ],
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "tags": {
                "environmentName": "[parameters('tagName')]",
                "test-account-rg": "true"
            }
        }
    ],
    "outputs": {
    }
}
```

## <a name="deploy-arm-template"></a>Déployer un modèle ARM

Vous pouvez déployer le modèle ARM à l’aide de deux options : PowerShell ou CLI.

L’exemple de code fourni ci-dessous utilise le modèle dans le sous-dossier « Templates » du sous-dossier « src ». Vous souhaiterez peut-être modifier le chemin d’accès de l’emplacement pour faire référence au fichier de modèle correctement.

Le processus de déploiement prend quelques minutes. Prenez note des noms du service FHIR et du groupe de ressources que vous utiliserez ultérieurement.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

### <a name="deploy-the-template-using-powershell"></a>Déployer le modèle : à l’aide de PowerShell

exécutez le code dans PowerShell localement, dans Visual Studio Code ou dans Azure Cloud Shell, pour déployer le service FHIR. 

si vous n’êtes pas connecté à Azure, utilisez « Connecter-AzAccount » pour vous connecter. Une fois que vous êtes connecté, utilisez « obtenir-AzContext » pour vérifier l’abonnement et le locataire que vous souhaitez utiliser. Vous pouvez modifier l’abonnement et le locataire si nécessaire.

Vous pouvez créer un groupe de ressources ou en utiliser un existant en ignorant l’étape ou en commentant la ligne commençant par « New-AzResourceGroup ».

```powershell-interactive
### variables
$resourcegroupname="your resource group"
$location="South Central US"
$workspacename="your workspace name"
$servicename="your fhir service name"
$tenantid="xxx"
$subscriptionid="xxx"
$storageaccountname="storage account name"
$storageaccountconfirm=1

### login to azure
Connect-AzAccount 
#Connect-AzAccount SubscriptionId $subscriptionid
Set-AzContext -Subscription $subscriptionid
Connect-AzAccount -Tenant $tenantid -SubscriptionId $subscriptionid
#Get-AzContext 

### create resource group
New-AzResourceGroup -Name $resourcegroupname -Location $location

### deploy the resource
New-AzResourceGroupDeployment -ResourceGroupName $resourcegroupname -TemplateFile "src/templates/fhirtemplate.json" -region $location -workspaceName $workspacename -fhirServiceName $fhirservicename -tenantid $tenantid -storageAccountName $storageaccountname -storageAccountConfirm $storageaccountconfirm
```
# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

### <a name="deploy-the-template-using-cli"></a>Déployer le modèle : à l’aide de l’interface CLI

exécutez le code localement, dans Visual Studio Code ou dans Azure Cloud Shell, pour déployer le service FHIR. 

Si vous n’êtes pas connecté à Azure, utilisez « AZ login » pour vous connecter. Une fois que vous êtes connecté, utilisez « AZ Account Show--output table » pour vérifier l’abonnement et le locataire que vous souhaitez utiliser. Vous pouvez modifier l’abonnement et le locataire si nécessaire.

Vous pouvez créer un groupe de ressources ou en utiliser un existant en ignorant l’étape ou en commentant la ligne commençant par « az Group Create ».

```azurecli-interactive
### variables
resourcegroupname=your resource group name
location=southcentralus
workspacename=your workspace name
fhirservicename=your fhir service name
tenantid=xxx
subscriptionid=xxx
storageaccountname=your storage account name
storageaccountconfirm=true

### login to azure
az login
az account show --output table
az account set --subscription $subscriptionid

### create resource group
az group create --name $resourcegroupname --location $location

### deploy the resource
az deployment group create --resource-group $resourcegroupname --template-file 'src\\templates\\fhirtemplate.json' --parameters region=$location workspaceName=$workspacename fhirServiceName=$fhirservicename tenantid=$tenantid storageAccountName=$storageaccountname storageAccountConfirm=$storageaccountconfirm
```

---

## <a name="review-the-deployed-resources"></a>Vérifier les ressources déployées

Vous pouvez vérifier que le service FHIR est opérationnel en ouvrant le navigateur et en accédant à `https://<yourfhir servic>.azurehealthcareapis.com/metadata` . Si l’instruction de fonctionnalité est affichée ou téléchargée automatiquement, votre déploiement est réussi. 

## <a name="clean-up-the-resources"></a>Nettoyer les ressources

Lorsque la ressource n’est plus nécessaire, exécutez le code ci-dessous pour supprimer le groupe de ressources.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)
```powershell-interactive
$resourceGroupName = “your resource group name”
Remove-AzResourceGroup -Name $resourceGroupName
```
# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)
```azurecli-interactive
resourceGroupName = “your resource group name”
az group delete --name $resourceGroupName
```
---

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé le service FHIR dans les API de santé Azure à l’aide d’un modèle ARM. Pour plus d’informations sur les fonctionnalités prises en charge par le service FHIR, consultez.

>[!div class="nextstepaction"]
>[Fonctionnalités prises en charge de FHIR](fhir-features-supported.md)