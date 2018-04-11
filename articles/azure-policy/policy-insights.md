---
title: Créer des stratégies et afficher les données de conformité par programmation avec Azure Policy | Microsoft Docs
description: Cet article vous explique comment créer et gérer des stratégies par programmation pour Azure Policy.
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/28/2018
ms.topic: article
ms.service: azure-policy
manager: carmonm
ms.custom: ''
ms.openlocfilehash: 1809f0b7ef386bb9eeaa55982178e4cd5e1dd2e2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Créer des stratégies et afficher les données de conformité par programmation avec Azure Policy

Cet article vous explique comment créer et gérer des stratégies par programmation. Il vous montre aussi comment afficher les états et les stratégies de conformité des ressources. Les définitions de stratégie appliquent différentes règles et actions sur vos ressources. Cette mise en conformité permet de garantir que les ressources restent conformes à vos normes d’entreprise et contrats de niveau de service.

## <a name="prerequisites"></a>Prérequis


Avant de commencer, vérifiez que les conditions préalables suivantes sont remplies :

1. Si ce n’est pas déjà fait, installez [ARMClient](https://github.com/projectkudu/ARMClient). C’est un outil qui envoie des requêtes HTTP aux API Azure Resource Manager.
2. Mettez à jour votre module AzureRM PowerShell vers la dernière version. Pour plus d’informations sur la version la plus récente, voir Azure PowerShell https://github.com/Azure/azure-powershell/releases.
3. Inscrivez le fournisseur de ressources Policy Insights à l’aide d’Azure PowerShell pour vous assurer que votre abonnement fonctionne avec ce fournisseur de ressources. Pour inscrire un fournisseur de ressources, vous devez être autorisé à effectuer l’opération d’inscription pour le fournisseur de ressources. Cette opération est incluse dans les rôles de contributeur et de propriétaire. Exécutez la commande suivante pour enregistrer le fournisseur de ressources :

    ```
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.PolicyInsights
    ```

    Pour plus d’informations sur l’inscription et l’affichage des fournisseurs de ressources, consultez la page [Fournisseurs et types de ressources](../azure-resource-manager/resource-manager-supported-services.md).

4. Si ce n’est déjà fait, installez Azure CLI. Vous pouvez obtenir la dernière version d’Azure CLI sur la page [Installation d’Azure CLI 2.0 sur Windows](/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-and-assign-a-policy-definition"></a>Créer et attribuer une définition de stratégie

Pour une meilleure visibilité de vos ressources, la première chose à faire est de créer et d’attribuer des stratégies à vos ressources. L’étape suivante consiste à apprendre à créer et assigner une stratégie par programmation. L’exemple de stratégie proposé réalise l’audit des comptes de stockage ouverts à tous les réseaux publics à l’aide de PowerShell, d’Azure CLI et de requêtes HTTP.

Les commandes suivantes créent des définitions de stratégie pour le niveau Standard. Le niveau Standard vous permet de procéder à grande échelle à la gestion, à l’évaluation de la conformité et à la correction. Pour plus d’informations sur les niveaux de tarification, consultez la page [Tarification Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy).

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Créer et assigner une définition de stratégie avec PowerShell

1. L’extrait de code JSON suivant permet de créer un fichier JSON nommé AuditStorageAccounts.json.

    ```
    {
    "if": {
      "allOf": [
        {
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

    ```
    PS C:\>New-AzureRmPolicyDefinition -Name "AuditStorageAccounts" -DisplayName "Audit Storage Accounts Open to Public Networks" -Policy C:\AuditStorageAccounts.json
    ```

    La commande crée une définition de stratégie nommée _Audit Storage Accounts Open to Public Networks_. Pour plus d’informations sur les autres paramètres que vous pouvez utiliser, consultez la page [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition?view=azurermps-4.4.1).

3. Après avoir créé votre définition de stratégie, vous pouvez créer une attribution de stratégie en exécutant les commandes suivantes :

    ```
$rg = Get-AzureRmResourceGroup -Name "ContosoRG"
```

    ```
$Policy = Get-AzureRmPolicyDefinition -Name "AuditStorageAccounts"
    ```

    ```
New-AzureRmPolicyAssignment -Name "AuditStorageAccounts" -PolicyDefinition $Policy -Scope $rg.ResourceId –Sku @{Name='A1';Tier='Standard'}
    ```

    Remplacez _ContosoRG_ par le nom de votre groupe de ressources prévu.

Pour plus d’informations sur la gestion des stratégies de ressources à l’aide du module Azure Resource Manager PowerShell, consultez [AzureRM.Resources](/powershell/module/azurerm.resources/?view=azurermps-4.4.1#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Créer et assigner une définition de stratégie avec ARMClient

Pour créer une définition de stratégie, procédez comme suit.

1. Copiez l’extrait de code JSON suivant pour créer un fichier JSON. Vous appellerez le fichier à l’étape suivante.

    ```
    {
    "properties": {
        "displayName": "Audit Storage Accounts Open to Public Networks",
        "policyType": "Custom",
        "mode": "Indexed",
        "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
        "parameters": {},
        "policyRule": {
              "if": {
                "allOf": [
                  {
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
}
```

2. Créez la définition de stratégie à l’aide de l’appel suivant :

    ```
    armclient PUT "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01 @<path to policy definition JSON file>"
    ```

    Remplacez preceding_ &lt;subscriptionId&gt; par l’ID de votre abonnement prévu.

Pour plus d’informations sur la structure de la requête, consultez [Définitions de stratégie – Créer ou mettre à jour](/rest/api/resources/policydefinitions/createorupdate).


Utilisez la procédure suivante pour créer une attribution de stratégie et assigner la définition de stratégie au niveau du groupe de ressources.

1. Copiez l’extrait de code JSON suivant pour créer un fichier d’attribution de stratégie JSON. Remplacez les informations de l’exemple entre &lt;&gt; par vos propres valeurs.

    ```
    {
  "properties": {
"description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
"displayName": "Audit Storage Accounts Open to Public Networks Assignment",
"parameters": {},
"policyDefinitionId":"/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
"scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
},
"sku": {
    "name": "A1",
    "tier": "Standard"
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

    ```
    {
                  "if": {
                    "allOf": [
                      {
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

    ```
az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
    ```

Pour créer une attribution de stratégie, utilisez la commande suivante. Remplacez les informations de l’exemple entre &lt;&gt; par vos propres valeurs.

```
az policy assignment create --name '<Audit Storage Accounts Open to Public Networks in Contoso RG' --scope '<scope>' --policy '<policy definition ID>' --sku 'standard'
```

Vous pouvez obtenir l’ID de définition de stratégie à l’aide de PowerShell avec la commande suivante :

```
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

L’ID de définition de stratégie pour la définition de stratégie que vous avez créée doit ressembler à ce qui suit :

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Pour plus d’informations sur la façon dont vous pouvez gérer les stratégies de ressources avec Azure CLI, consultez [Stratégies de ressources Azure CLI](/cli/azure/policy?view=azure-cli-latest).

## <a name="identify-non-compliant-resources"></a>Identifier les ressources non conformes

Dans une attribution, une ressource est dite non conforme si elle ne respecte pas les règles de l’initiative ou de la stratégie. Le tableau suivant montre comment différentes actions de stratégie fonctionnent avec l’évaluation des conditions pour l’état de conformité qui en résulte :

| **État de la ressource** | **Action** | **Évaluation de la stratégie** | **État de conformité** |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Non conforme |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Conforme |
| Nouveau | Audit, AuditIfNotExist\* | True | Non conforme |
| Nouveau | Audit, AuditIfNotExist\* | False | Conforme |

\* Les actions Append, DeployIfNotExist et AuditIfNotExist nécessitent que l’instruction IF ait la valeur TRUE. Les actions nécessitent également la condition FALSE pour être non conformes. Lorsque la valeur est TRUE, la condition IF déclenche l’évaluation de la condition d’existence pour les ressources associées.

Pour mieux comprendre le marquage des ressources comme étant non conformes, nous allons utiliser l’exemple d’attribution de stratégie créé précédemment.

Supposons, par exemple, que vous disposiez d’un groupe de ressources (ContosoRG), comprenant des comptes de stockage (en rouge) qui sont exposés sur des réseaux publics.

![Comptes de stockage exposés sur des réseaux publics](./media/policy-insights/resource-group01.png)

Dans cet exemple, vous devez faire attention aux risques de sécurité. Maintenant que vous avez créé une attribution de stratégie, elle est évaluée pour tous les comptes de stockage du groupe de ressources ContosoRG. Elle effectue l’audit des trois comptes de stockage non conformes et en modifie donc l’état pour afficher un état **non conforme.**

![Audit des comptes de stockage non conformes](./media/policy-insights/resource-group03.png)

Procédez comme suit pour identifier les ressources d’un groupe de ressources qui ne sont pas conformes à l’attribution de stratégie. Dans cet exemple, les ressources sont des comptes de stockage du groupe de ressources ContosoRG.

1. Récupérez l’ID d’attribution de stratégie en exécutant la commande suivante :

    ```
    $policyAssignment = Get-AzureRmPolicyAssignment | where {$_.properties.displayName -eq "Audit Storage Accounts with Open Public Networks"}
    ```

    ```
    $policyAssignment.PolicyAssignmentId
    ```

    Pour savoir comment obtenir un ID d’attribution de stratégie, consultez la page [Get-AzureRMPolicyAssignment](https://docs.microsoft.com/en-us/powershell/module/azurerm.resources/Get-AzureRmPolicyAssignment?view=azurermps-4.4.1).

2. Exécutez la commande suivante pour que les ID des ressources non conformes soient copiés dans un fichier JSON :

    ```
    armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
    ```

3. Les résultats doivent ressembler à ce qui suit :

  ```
      {
  "@odata.context":"https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
  "@odata.count": 3,
  "value": [
  {
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount1Id>"
      },
      {
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount2Id>"
             },
  {
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount3ID>"
             }
  ]
  }
  ```

Les résultats sont comparables à ce que vous devriez généralement voir sous **Ressources non conformes** dans la [vue du portail Azure](assign-policy-definition.md#identify-non-compliant-resources).

À l’heure actuelle, les ressources non conformes sont uniquement identifiées à l’aide du portail Azure et des requêtes HTTP. Pour plus d’informations sur l’interrogation des états de stratégie, consultez l’article de référence sur l’API [État de la stratégie](/rest/api/policy-insights/policystates).

## <a name="view-policy-events"></a>Afficher les événements de stratégie

Lorsqu’une ressource est créée ou mise à jour, un résultat d’évaluation de stratégie est généré. Ces résultats sont appelés _événements de stratégie_. Exécutez la requête suivante pour afficher tous les événements de stratégie associés à l’attribution de stratégie.

```
armclient POST "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2017-12-12-preview"
```

Vos résultats doivent ressembler à l’exemple suivant :

```
{
  "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
  "@odata.count": 1,
  "value": [
    {
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
      "NumAuditEvents": 3
    }
  ]
}

```

Comme pour les états de stratégie, vous pouvez uniquement afficher les événements de stratégie avec des requêtes HTTP. Pour plus d’informations sur l’interrogation des événements de stratégie, consultez l’article de référence intitulé [Événements de stratégie](/rest/api/policy-insights/policyevents).

## <a name="change-a-policy-assignments-pricing-tier"></a>Modifier le niveau tarifaire d’une attribution de stratégie

Vous pouvez utiliser la cmdlet PowerShell *Set-AzureRmPolicyAssignment* afin de passer au niveau de tarification Standard ou Gratuit pour une attribution de stratégie existante. Par exemple : 

```
Set-AzureRmPolicyAssignment -Id /subscriptions/<subscriptionId/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/<policyAssignmentID> -Sku @{Name='A1';Tier='Standard'}
```

Pour plus d’informations sur la cmdlet, consultez la page [Set-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/Set-AzureRmPolicyAssignment?view=azurermps-4.4.1).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les commandes et les requêtes de cet article, consultez les articles suivants.

- [Ressources API REST Azure](/rest/api/resources/)
- [Modules Azure RM PowerShell](/powershell/module/azurerm.resources/?view=azurermps-4.4.1#policies)
- [Commandes de stratégie Azure CLI](/cli/azure/policy?view=azure-cli-latest)
- [Informations de référence sur l’API REST du fournisseur de ressources Policy Insights](/rest/api/policy-insights)
