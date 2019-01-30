---
title: Clé secrète de coffre de clés avec un modèle Azure Resource Manager | Microsoft Docs
description: Montre comment passer une clé secrète à partir d’un coffre de clés en tant que paramètre lors du déploiement.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: e04ee6b74be0bb7ec81166f43295a8a9377f8cf8
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54381516"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement

Lorsque vous avez besoin de passer une valeur sécurisée (par exemple, un mot de passe) comme paramètre au cours du déploiement, vous pouvez récupérer la valeur à partir d’un coffre [Azure Key Vault](../key-vault/key-vault-whatis.md). Vous récupérez la valeur en référençant le coffre de clés et la clé secrète dans votre fichier de paramètres. La valeur n’est jamais exposée, car vous référencez uniquement son ID de coffre de clés. Le coffre de clés peut exister dans un autre abonnement que le groupe de ressources sur lequel vous effectuez le déploiement.

## <a name="deploy-a-key-vault-and-secret"></a>Déploiement d'un coffre de clés et d’une clé secrète

Pour créer un coffre de clés et une clé secrète, utilisez l’interface de ligne de commande Azure CLI ou PowerShell. `enabledForTemplateDeployment` est une propriété du coffre de clés. Pour accéder aux secrets à l’intérieur de ce Key Vault à partir d’un déploiement Resource Manager, `enabledForTemplateDeployment` doit être `true`. 

Les exemples suivants de scripts Azure PowerShell et Azure CLI montrent comment créer un Key Vault et un secret.

Pour l’interface de ligne de commande Azure, consultez :

```azurecli-interactive
keyVaultName='{your-unique-vault-name}'
resourceGroupName='{your-resource-group-name}'
location='centralus'
userPrincipalName='{your-email-address-associated-with-your-subscription}'

# Create a resource group
az group create --name $resourceGroupName --location $location

# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

# Create a secret with the name, vmAdminPassword
password=$(openssl rand -base64 32)
echo $password
az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
```

Pour PowerShell, utilisez la commande suivante :

```azurepowershell-interactive
$keyVaultName = "{your-unique-vault-name}"
$resourceGroupName="{your-resource-group-name}"
$location='Central US'
$userPrincipalName='{your-email-address-associated-with-your-subscription}'

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

New-AzureRmKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list

$password = openssl rand -base64 32
echo $password
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretvalue
```

Si vous exécutez le script PowerShell en dehors de Cloud Shell, utilisez la commande suivante pour générer le mot de passe :

```powershell
Add-Type -AssemblyName System.Web
[System.Web.Security.Membership]::GeneratePassword(16,3)
```

Utilisation du modèle Resource Manager : Consultez le [tutoriel : Intégrer Azure Key Vault à un déploiement de modèle Resource Manager](./resource-manager-tutorial-use-key-vault.md#prepare-the-key-vault).

> [!NOTE]
> Chaque service Azure présente des exigences de mot de passe spécifiques. Par exemple, les exigences de la machine virtuelle Azure sont disponibles dans [Quelles sont les exigences en matière de mot de passe lors de la création d’une machine virtuelle ?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="enable-access-to-the-secret"></a>Autoriser l’accès à la clé secrète

En plus de paramétrer `enabledForTemplateDeployment` sur `true`, l’utilisateur déployant le modèle doit disposer de l’autorisation `Microsoft.KeyVault/vaults/deploy/action` pour l’étendue qui contient le coffre de clés, y compris le groupe de ressources et Key Vault. Les rôles [propriétaire](../role-based-access-control/built-in-roles.md#owner) et [contributeur](../role-based-access-control/built-in-roles.md#contributor) accordent cet accès. Si vous créez le coffre de clés, vous êtes le propriétaire et avez donc l’autorisation. Si le coffre de clés est sous un autre abonnement, le propriétaire du coffre de clés doit vous donner l’accès.

La procédure suivante montre comment créer un rôle avec les permissions minimales et comment affecter l’utilisateur
1. Créez un fichier JSON de définition de rôle personnalisé :

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    Remplacez « 00000000-0000-0000-0000-000000000000 » par l’ID d’abonnement de l’utilisateur qui souhaite déployer les modèles.

2. Créez le nouveau rôle à l’aide du fichier JSON :

    ```azurepowershell
    $resourceGroupName= "<Resource Group Name>" # the resource group which contains the Key Vault
    $userPrincipalName = "<Email Address of the deployment operator>"
    New-AzureRmRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzureRmRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    L’exemple `New-AzureRmRoleAssignment` attribue le rôle personnalisé à l’utilisateur au niveau du groupe de ressources.  

Quand vous utilisez un coffre de clés avec le modèle pour une [Application managée](../managed-applications/overview.md), vous devez accorder l’accès au principal de service du **fournisseur de ressources d’appliance**. Pour plus d’informations, consultez [Accéder au secret de coffre de clés pendant le déploiement d’applications managées Azure](../managed-applications/key-vault-access.md).

## <a name="reference-a-secret-with-static-id"></a>Référencement d’un secret avec un ID statique

Le modèle qui reçoit un secret de coffre de clés est similaire à n’importe quel modèle. Ceci s’explique par le fait que **vous référencez le coffre de clés dans le fichier de paramètres, et non dans le modèle.** L’illustration suivante montre comment le fichier de paramètres fait référence à la question secrète et passe cette valeur au modèle.

![ID statique](./media/resource-manager-keyvault-parameter/statickeyvault.png)

Par exemple, le [modèle suivant](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) déploie une base de données SQL qui comprend un mot de passe administrateur. Le paramètre du mot de passe est défini sur une chaîne sécurisée. Toutefois, le modèle ne spécifie pas d’où vient cette valeur.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

À présent, créez un fichier de paramètres pour le modèle précédent. Dans le fichier de paramètres, spécifiez un paramètre qui correspond au nom du paramètre dans le modèle. Pour la valeur du paramètre, référencez le secret du coffre de clés. Vous référencez la clé secrète en passant l'identificateur de ressource du coffre de clés et le nom de la clé secrète. Dans le [fichier de paramètres suivant](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.parameters.json), le secret du coffre de clés doit déjà exister, et vous définissez une valeur statique pour son ID de ressource. Copiez ce fichier localement et définissez l’ID d’abonnement, le nom du coffre et le nom du serveur SQL.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "examplesecret"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

Si vous devez utiliser une version du secret autre que la version actuelle, utilisez la propriété `secretVersion`.

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Maintenant, déployez le modèle et transmettez le fichier de paramètres. Vous pouvez vous servir de l’exemple de modèle de GitHub, à condition d’utiliser un fichier de paramètres régionaux dont les valeurs sont définies pour votre environnement.

Pour l’interface de ligne de commande Azure, consultez :

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

Pour PowerShell, utilisez la commande suivante :

```powershell-interactive
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>Référencement d’un secret avec un ID dynamique

La section précédente expliquait comment transmettre un ID de ressource statique pour la clé secrète du coffre de clés à partir du paramètre. Toutefois, dans certains scénarios, vous devez référencer une clé secrète de coffre de clés qui varie selon le déploiement actuel. Ou bien, vous pouvez souhaiter transmettre simplement des valeurs de paramètre au modèle au lieu de créer un paramètre de référence dans le fichier de paramètres. Dans les deux cas, vous pouvez générer dynamiquement l’ID de ressource pour un secret de coffre de clés à l’aide d’un modèle lié.

Vous ne pouvez pas générer dynamiquement l’ID de ressource dans le fichier de paramètres, car les expressions de modèle ne sont pas autorisées dans ce dernier. 

Dans votre modèle parent, ajoutez le modèle lié et passez un paramètre qui contient l’ID de ressource généré dynamiquement. L’illustration suivante montre comment un paramètre dans le modèle lié fait référence au secret.

![ID dynamique](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

Le [modèle suivant](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id) crée de façon dynamique l’ID de coffre de clés et le passe en tant que paramètre.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "The location where the resources will be deployed."
            }
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "The name of the keyvault that contains the secret."
            }
        },
        "secretName": {
            "type": "string",
            "metadata": {
                "description": "The name of the secret."
            }
        },
        "vaultResourceGroupName": {
            "type": "string",
            "metadata": {
                "description": "The name of the resource group that contains the keyvault."
            }
        },
        "vaultSubscription": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "The name of the subscription that contains the keyvault."
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located. When the template is deployed using the accompanying scripts, a private location in the subscription will be used and this value will be automatically generated."
            },
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/"
        },
        "_artifactsLocationSasToken": {
            "type": "securestring",
            "metadata": {
                "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated."
            },
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "[uri(parameters('_artifactsLocation'), concat('./nested/sqlserver.json', parameters('_artifactsLocationSasToken')))]"
                },
                "parameters": {
                    "location": {
                        "value": "[parameters('location')]"
                    },
                    "adminLogin": {
                        "value": "ghuser"
                    },
                    "adminPassword": {
                        "reference": {
                            "keyVault": {
                                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                            },
                            "secretName": "[parameters('secretName')]"
                        }
                    }
                }
            }
        }
    ],
    "outputs": {
        "sqlFQDN": {
            "type": "string",
            "value": "[reference('dynamicSecret').outputs.sqlFQDN.value]"
        }
    }
}
```

Déployez le modèle précédent et fournissez des valeurs pour les paramètres. Vous pouvez vous servir de l’exemple de modèle de GitHub, mais vous devez fournir des valeurs de paramètres pour votre environnement.

Pour l’interface de ligne de commande Azure, consultez :

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=<your-vault> vaultResourceGroupName=examplegroup secretName=examplesecret
```

Pour PowerShell, utilisez la commande suivante :

```powershell
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName <your-vault> -vaultResourceGroupName examplegroup -secretName examplesecret
```

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir des informations générales sur les coffres de clés, consultez [Prise en main d’Azure Key Vault](../key-vault/key-vault-get-started.md).
* Pour obtenir des exemples complets de référencement de clés secrètes, consultez [Exemples de coffres de clés](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).