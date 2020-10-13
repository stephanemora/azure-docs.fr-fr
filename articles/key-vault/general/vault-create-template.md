---
title: Guide Azure - Créer un coffre de clés et une stratégie d’accès à l’aide d’un modèle Azure Resource Manager | Microsoft Docs
description: Présente comment créer des coffres de clés Azure et des stratégies d’accès à l’aide d’un modèle Azure Resource Manager.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: cf19561005fe2e98b7b5cf6812ff9224fd9474dc
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804253"
---
# <a name="how-to-create-azure-key-vault-and-vault-access-policy-using-a-resource-manager-template"></a>Comment créer des coffres de clés Azure et des stratégies d’accès à l’aide d’un modèle Azure Resource Manager

[Azure Key Vault](../general/overview.md) est un service cloud qui propose un magasin sécurisé pour les secrets, comme les clés, les mots de passe, les certificats et autres secrets. Ce guide porte essentiellement sur le processus de déploiement d’un modèle ARM (Azure Resource Manager) en vue de créer un coffre de clés.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article :

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.


## <a name="create-key-vault-resource-manager-template"></a>Créer un modèle Resource Manager de coffre de clés

Le modèle suivant montre comment créer un coffre de clés de façon très simple. Certaines valeurs sont spécifiées dans le modèle.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    }
  ]
}

```

Pour plus d’informations sur les paramètres du modèle de coffre de clés, consultez [Référence sur le modèle ARM de coffre de clés](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> Si le modèle est redéployé, il remplace toutes les stratégies d’accès existantes dans le coffre de clés. Il est recommandé de remplir la `accessPolicies` propriété avec les stratégies d’accès existantes pour éviter de perdre l’accès au coffre de clés. 

## <a name="add-access-policy-to-key-vault-resource-manager-template"></a>Ajouter une stratégie d’accès au modèle Resource Manager de coffre de clés

Vous pouvez déployer des stratégies d’accès à un coffre de clés existant sans redéploiement de l’intégralité du modèle de coffre de clés. Le modèle suivant montre comment créer des stratégies d’accès.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": [parameters('certificatesPermissions')]
            }
          }
        ]
      }
    }
  ]
}

```
Pour plus d’informations sur les paramètres du modèle de coffre de clés, consultez [Référence sur le modèle ARM de coffre de clés](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="other-available-key-vault-resource-manager-templates"></a>Autres modèles Resource Manager de coffre de clés

Il existe d’autres modèles Resource Manager pour les objets Key Vault :

| Secrets | Keys | Certificats |
|--|--|--|
|[Démarrage rapide](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-template)<br>[Référence](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/secrets)|N/A|N/A|

Autres modèles de Key Vault : [Référence Resource Manager pour Key Vault](https://docs.microsoft.com/azure/templates/microsoft.keyvault/allversions)

## <a name="deploy-the-templates"></a>Déployer les modèles

Vous pouvez utiliser le Portail Azure pour déployer les modèles ci-dessus à l’aide de l’option « Créer votre propre modèle dans l’éditeur » dans le guide ci-dessous : [Déployer des ressources à partir d’un modèle personnalisé](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template)

Vous pouvez également enregistrer les modèles ci-dessus dans des fichiers et utiliser les commandes suivantes :  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) et [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) :

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous prévoyez d’utiliser d’autres démarrages rapides et tutoriels, il peut être utile de conserver ces ressources. Si vous n’en avez plus besoin, supprimez le groupe de ressources. Ce faisant, vous supprimez le coffre de clés et les ressources associées. Pour supprimer le groupe de ressources avec Azure CLI ou Azure PowerShell, procédez comme suit.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="resources"></a>Ressources

- Lire la [vue d’ensemble Azure Key Vault](../general/overview.md)
- En savoir plus sur [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Passer en revue les [bonnes pratiques relatives à Azure Key Vault](../general/best-practices.md)

## <a name="next-steps"></a>Étapes suivantes

- [Sécuriser l’accès à un coffre de clés](secure-your-key-vault.md)
- [S’authentifier auprès d’un coffre de clés](authentication.md)
- [Guide du développeur Azure Key Vault](developers-guide.md)
