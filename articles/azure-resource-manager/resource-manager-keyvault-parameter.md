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
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 93b92a8a3b8aacd1f665725643314858fe92ad3c
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233766"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement

Au lieu de placer une valeur sécurisée (telle qu'un mot de passe) directement dans votre fichier de paramètres, vous pouvez récupérer la valeur à partir d'un coffre [Azure Key Vault](../key-vault/key-vault-whatis.md) pendant un déploiement. Vous récupérez la valeur en référençant le coffre de clés et la clé secrète dans votre fichier de paramètres. La valeur n’est jamais exposée, car vous référencez uniquement son ID de coffre de clés. Le coffre de clés peut exister dans un autre abonnement que le groupe de ressources sur lequel vous effectuez le déploiement.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-key-vaults-and-secrets"></a>Déployer des coffres de clés et des secrets

Pour créer des coffres de clés et ajouter des secrets, consultez :

- [Définir et récupérer un secret à l'aide de l'interface de ligne de commande (CLI)](../key-vault/quick-create-cli.md)
- [Définir et récupérer un secret à l'aide de Powershell](../key-vault/quick-create-powershell.md)
- [Définir et récupérer un secret à l'aide du portail](../key-vault/quick-create-portal.md)
- [Définir et récupérer un secret à l'aide de .NET](../key-vault/quick-create-net.md)
- [Définir et récupérer un secret à l'aide de Node.js](../key-vault/quick-create-node.md)

Quelques considérations et exigences supplémentaires doivent être prises en compte lors de l'intégration d'un coffre de clés avec un déploiement de modèles Resource Manager :

- `enabledForTemplateDeployment` est une propriété du coffre de clés. Pour accéder aux secrets à l’intérieur de ce Key Vault à partir d’un déploiement Resource Manager, `enabledForTemplateDeployment` doit être `true`. 
- Si le coffre de clés ne vous appartient pas, son propriétaire doit mettre à jour les paramètres de la stratégie de sécurité du coffre de clés pour vous permettre d'ajouter des secrets.

Les exemples Azure CLI et Azure PowerShell suivants illustrent cette procédure :

```azurecli
# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list
```

```azurepowershell
New-AzKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list
```

## <a name="grant-access-to-the-secrets"></a>Accorder l'accès aux secrets

L'utilisateur qui déploie le modèle doit disposer de l'autorisation `Microsoft.KeyVault/vaults/deploy/action` pour l'étendue qui contient le coffre de clés, y compris le groupe de ressources et Key Vault. Les rôles [propriétaire](../role-based-access-control/built-in-roles.md#owner) et [contributeur](../role-based-access-control/built-in-roles.md#contributor) accordent cet accès. Si vous créez le coffre de clés, vous êtes le propriétaire et avez donc l’autorisation. Si le coffre de clés est sous un autre abonnement, le propriétaire du coffre de clés doit vous donner l’accès.

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
    New-AzRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    L'exemple `New-AzRoleAssignment` attribue le rôle personnalisé à l'utilisateur au niveau du groupe de ressources.  

Quand vous utilisez un coffre de clés avec le modèle pour une [Application managée](../managed-applications/overview.md), vous devez accorder l’accès au principal de service du **fournisseur de ressources d’appliance**. Pour plus d’informations, consultez [Accéder au secret de coffre de clés pendant le déploiement d’applications managées Azure](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Référencement de secrets avec un ID statique

Avec cette approche, vous référencez le coffre de clés dans le fichier de paramètres, et non dans le modèle. L’illustration suivante montre comment le fichier de paramètres fait référence à la question secrète et passe cette valeur au modèle.

![Diagramme d'ID statique d'intégration d'un coffre de clés à Resource Manager](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Tutoriel : Intégrer Azure Key Vault à un déploiement de modèle Resource Manager](./resource-manager-tutorial-use-key-vault.md) utilise cette méthode. Le tutoriel déploie une machine virtuelle avec mot de passe administrateur. Le paramètre du mot de passe est défini sur une chaîne sécurisée :

![Fichier de modèle d'ID statique d'intégration d'un coffre de clés à Resource Manager](./media/resource-manager-keyvault-parameter/resource-manager-key-vault-static-id-template-file.png)

À présent, créez un fichier de paramètres pour le modèle précédent. Dans le fichier de paramètres, spécifiez un paramètre qui correspond au nom du paramètre dans le modèle. Pour la valeur du paramètre, référencez le secret du coffre de clés. Vous référencez le secret en transmettant l'identificateur de ressource du coffre de clés et le nom du secret :

![Fichier de paramètres d'ID statique d'intégration d'un coffre de clés à Resource Manager](./media/resource-manager-keyvault-parameter/resource-manager-key-vault-static-id-parameter-file.png)

Si vous devez utiliser une version du secret autre que la version actuelle, utilisez la propriété `secretVersion`.

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Déployez le modèle et transmettez le fichier de paramètres :

Pour l’interface de ligne de commande Azure, consultez :

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri <The Template File URI> \
    --parameters <The Parameter File>
```

Pour PowerShell, utilisez la commande suivante :

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <The Template File URI> `
  -TemplateParameterFile <The Parameter File>
```

## <a name="reference-secrets-with-dynamic-id"></a>Référencement de secrets avec un ID dynamique

La section précédente expliquait comment transmettre un ID de ressource statique pour la clé secrète du coffre de clés à partir du paramètre. Toutefois, dans certains scénarios, vous devez référencer une clé secrète de coffre de clés qui varie selon le déploiement actuel. Ou bien, vous pouvez souhaiter transmettre des valeurs de paramètre au modèle au lieu de créer un paramètre de référence dans le fichier de paramètres. Dans les deux cas, vous pouvez générer dynamiquement l’ID de ressource pour un secret de coffre de clés à l’aide d’un modèle lié.

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

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=$keyVaultName vaultResourceGroupName=examplegroup secretName=examplesecret
```

Pour PowerShell, utilisez la commande suivante :

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName $keyVaultName -vaultResourceGroupName $keyVaultResourceGroupName -secretName $secretName
```

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations générales sur les coffres de clés, consultez [Présentation d’Azure Key Vault](../key-vault/key-vault-overview.md).
- Pour obtenir des exemples complets de référencement de clés secrètes, consultez [Exemples de coffres de clés](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
