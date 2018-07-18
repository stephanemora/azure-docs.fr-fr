---
title: Créer des stratégies et afficher les données de conformité par programmation avec Azure Policy
description: Cet article vous explique comment créer et gérer des stratégies par programmation pour Azure Policy.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: a83402316854b23fe85bff813dc9f5665bccd1fb
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794807"
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Créer des stratégies et afficher les données de conformité par programmation avec Azure Policy

Cet article vous explique comment créer et gérer des stratégies par programmation. Il vous montre aussi comment afficher les états et les stratégies de conformité des ressources. Les définitions de stratégie appliquent différentes règles et des différents effets sur vos ressources. Cette mise en conformité permet de garantir que les ressources restent conformes à vos normes d’entreprise et contrats de niveau de service.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que les conditions préalables suivantes sont remplies :

1. Si ce n’est pas déjà fait, installez [ARMClient](https://github.com/projectkudu/ARMClient). C’est un outil qui envoie des requêtes HTTP aux API Azure Resource Manager.
2. Mettez à jour votre module AzureRM PowerShell vers la dernière version. Pour plus d’informations sur la version la plus récente, voir [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).
3. Inscrivez le fournisseur de ressources Policy Insights à l’aide d’Azure PowerShell pour vous assurer que votre abonnement fonctionne avec ce fournisseur de ressources. Pour inscrire un fournisseur de ressources, vous devez être autorisé à effectuer l’opération d’inscription pour le fournisseur de ressources. Cette opération est incluse dans les rôles de contributeur et de propriétaire. Exécutez la commande suivante pour enregistrer le fournisseur de ressources :

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Pour plus d’informations sur l’inscription et l’affichage des fournisseurs de ressources, consultez la page [Fournisseurs et types de ressources](../azure-resource-manager/resource-manager-supported-services.md).
4. Si ce n’est déjà fait, installez Azure CLI. Vous pouvez obtenir la dernière version d’Azure CLI sur la page [Installation d’Azure CLI 2.0 sur Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Créer et attribuer une définition de stratégie

Pour une meilleure visibilité de vos ressources, la première chose à faire est de créer et d’attribuer des stratégies à vos ressources. L’étape suivante consiste à apprendre à créer et assigner une stratégie par programmation. L’exemple de stratégie proposé réalise l’audit des comptes de stockage ouverts à tous les réseaux publics à l’aide de PowerShell, d’Azure CLI et de requêtes HTTP.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Créer et assigner une définition de stratégie avec PowerShell

1. L’extrait de code JSON suivant permet de créer un fichier JSON nommé AuditStorageAccounts.json.

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

  Pour plus d’informations sur la création d’une définition de stratégie, consultez la page [Structure de définition Azure Policy](policy-definition.md).
2. Exécutez la commande suivante pour créer une définition de stratégie en utilisant le fichier AuditStorageAccounts.json.

  ```azurepowershell-interactive
  New-AzureRmPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
  ```

  La commande crée une définition de stratégie nommée _Audit Storage Accounts Open to Public Networks_. Pour plus d’informations sur les autres paramètres que vous pouvez utiliser, consultez la page [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition).
3. Après avoir créé votre définition de stratégie, vous pouvez créer une attribution de stratégie en exécutant les commandes suivantes :

  ```azurepowershell-interactive
  $rg = Get-AzureRmResourceGroup -Name 'ContosoRG'
  $Policy = Get-AzureRmPolicyDefinition -Name 'AuditStorageAccounts'
  New-AzureRmPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
  ```

  Remplacez _ContosoRG_ par le nom de votre groupe de ressources prévu.

Pour plus d’informations sur la gestion des stratégies de ressources à l’aide du module Azure Resource Manager PowerShell, consultez [AzureRM.Resources](/powershell/module/azurerm.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Créer et assigner une définition de stratégie avec ARMClient

Pour créer une définition de stratégie, procédez comme suit.

1. Copiez l’extrait de code JSON suivant pour créer un fichier JSON. Vous appellerez le fichier à l’étape suivante.

  ```json
  "properties": {
      "displayName": "Audit Storage Accounts Open to Public Networks",
      "policyType": "Custom",
      "mode": "Indexed",
      "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
      "parameters": {},
      "policyRule": {
          "if": {
              "allOf": [{
                      "field": "type",
                      "equals": "Microsoft.Storage/storageAccounts"
                  },
                  {
                      "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                      "equals": "Allow"
                  }
              ]
          },
          "then": {
              "effect": "audit"
          }
      }
  }
  ```

2. Créez la définition de stratégie à l’aide de l’un des appels suivants :

  ```
  # For defining a policy in a subscription
  armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

  # For defining a policy in a management group
  armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
  ```

  Remplacez {subscriptionId} précédent par l’ID de votre abonnement ou {managementGroupId} par l’ID de votre [groupe d’administration](../azure-resource-manager/management-groups-overview.md).

  Pour plus d’informations sur la structure de la requête, consultez [Définitions de stratégie – Créer ou mettre à jour](/rest/api/resources/policydefinitions/createorupdate) et [Définitions de stratégie – Créer ou mettre à jour dans le groupe d’administration](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

Utilisez la procédure suivante pour créer une attribution de stratégie et assigner la définition de stratégie au niveau du groupe de ressources.

1. Copiez l’extrait de code JSON suivant pour créer un fichier d’attribution de stratégie JSON. Remplacez les informations de l’exemple entre &lt;&gt; par vos propres valeurs.

  ```json
  {
      "properties": {
          "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
          "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
          "parameters": {},
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
          "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
      }
  }
  ```

2. Créez l’attribution de stratégie à l’aide de l’appel suivant :

  ```
  armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
  ```

  Remplacez les informations de l’exemple entre &lt;&gt; par vos propres valeurs.

  Pour plus d’informations sur les appels HTTP à l’API REST, consultez la page [Ressources API REST Azure](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Créer et attribuer une définition de stratégie avec Azure CLI

Pour créer une définition de stratégie, procédez comme suit :

1. Copiez l’extrait de code JSON suivant pour créer un fichier d’attribution de stratégie JSON.

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

2. Pour créer une définition de stratégie, exécutez la commande suivante :

  ```azurecli-interactive
az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
  ```

3. Pour créer une attribution de stratégie, utilisez la commande suivante. Remplacez les informations de l’exemple entre &lt;&gt; par vos propres valeurs.

  ```azurecli-interactive
  az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
  ```

Vous pouvez obtenir l’ID de définition de stratégie à l’aide de PowerShell avec la commande suivante :

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

L’ID de définition de stratégie pour la définition de stratégie que vous avez créée doit ressembler à ce qui suit :

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Pour plus d’informations sur la façon dont vous pouvez gérer les stratégies de ressources avec Azure CLI, consultez [Stratégies de ressources Azure CLI](/cli/azure/policy?view=azure-cli-latest).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les commandes et les requêtes de cet article, consultez les articles suivants.

- [Ressources API REST Azure](/rest/api/resources/)
- [Modules Azure RM PowerShell](/powershell/module/azurerm.resources/#policies)
- [Commandes de stratégie Azure CLI](/cli/azure/policy?view=azure-cli-latest)
- [Informations de référence sur l’API REST du fournisseur de ressources Policy Insights](/rest/api/policy-insights)
- [Organiser vos ressources avec des groupes d’administration Azure](../azure-resource-manager/management-groups-overview.md)