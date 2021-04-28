---
title: Afficher les journaux d’activité pour voir les changements RBAC Azure
description: Affichez les journaux d’activité des changements de contrôle d’accès en fonction du rôle Azure (RBAC Azure) apportés au cours des 90 derniers jours.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: rolyon
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 625cf443ab9bd334d2a10e3fb15348459b85f625
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108069990"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Afficher les journaux d’activité pour voir les changements RBAC Azure

Vous avez parfois besoin d’informations sur les modifications du contrôle d’accès en fonction du rôle Azure (RBAC Azure), par exemple à des fins d’audit ou de dépannage. Quand un utilisateur apporte des changements à des attributions ou des définitions de rôle au sein de vos abonnements, ceux-ci sont journalisés dans la catégorie [Journal d’activité Azure](../azure-monitor/essentials/platform-logs-overview.md). Vous pouvez afficher les journaux d’activité pour voir tous les changements RBAC Azure des 90 derniers jours.

## <a name="operations-that-are-logged"></a>Opérations journalisées

Voici les opérations RBAC Azure qui sont journalisées dans le journal d’activité :

- Créer une attribution de rôle
- Supprimer une attribution de rôle
- Créer ou mettre à jour une définition de rôle personnalisée
- Supprimer la définition de rôle personnalisée

## <a name="azure-portal"></a>Portail Azure

Pour commencer, le plus simple consiste à afficher les journaux d’activité avec le portail Azure. La capture d’écran suivante montre un exemple d’opérations d’attribution de rôle dans le journal d’activité. Elle contient également une option pour télécharger les journaux dans un fichier CSV.

![Journaux d’activité à l’aide du portail : capture d’écran](./media/change-history-report/activity-log-portal.png)

Pour obtenir plus d’informations, cliquez sur une entrée pour ouvrir le volet Résumé. Cliquez sur l’onglet **JSON** pour obtenir un journal détaillé.

![Journaux d’activité utilisant le portail avec le volet Résumé ouvert – capture d’écran](./media/change-history-report/activity-log-summary-portal.png)

Le journal d’activité dans le portail comporte plusieurs filtres. Voici les filtres RBAC Azure :

| Filtrer | Valeur |
| --------- | --------- |
| Catégorie d'événements | <ul><li>Administratif</li></ul> |
| Opération | <ul><li>Créer une attribution de rôle</li><li>Supprimer une attribution de rôle</li><li>Créer ou mettre à jour une définition de rôle personnalisée</li><li>Supprimer la définition de rôle personnalisée</li></ul> |

Pour plus d’informations sur les journaux d’activité, consultez [Afficher les journaux d’activité pour surveiller les actions sur les ressources](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).


## <a name="interpret-a-log-entry"></a>Interpréter une entrée de journal

La sortie de journal à partir de l’onglet JSON, d’Azure PowerShell ou d’Azure CLI peut inclure un grand nombre d’informations. Voici quelques-unes des propriétés clés à rechercher lors de la tentative d’interpréter une entrée de journal. Pour plus d’informations sur les méthodes de filtrage de la sortie de journal à l’aide d’Azure PowerShell ou d’Azure CLI, consultez les sections suivantes.

> [!div class="mx-tableFixed"]
> | Propriété | Exemples de valeurs | Description |
> | --- | --- | --- |
> | autorisation:action | Microsoft.Authorization/roleAssignments/write | Créer une attribution de rôle |
> |  | Microsoft.Authorization/roleAssignments/delete | Supprimer une attribution de rôle |
> |  | Microsoft.Authorization/roleDefinitions/write | Créer ou mettre à jour une définition de rôle |
> |  | Microsoft.Authorization/roleDefinitions/delete | Supprimer une définition de rôle |
> | autorisation:étendue | /subscriptions/{subscriptionId}<br/>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId} | Étendue de l’action |
> | caller | admin@example.com<br/>{objectId} | Qui a initié l’action |
> | eventTimestamp | 2021-03-01T22:07:41.126243Z | Heure à laquelle l’action s’est produite |
> | état:valeur | Démarré<br/>Opération réussie<br/>Échec | État de l’action |

## <a name="azure-powershell"></a>Azure PowerShell

Pour afficher les journaux d’activité avec Azure PowerShell, utilisez la commande [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog).

Cette commande liste tous les changements d’attribution de rôle dans un abonnement au cours des sept derniers jours :

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Cette commande liste tous les changements de définition de rôle dans un groupe de ressources au cours des sept derniers jours :

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

### <a name="filter-log-output"></a>Filtrer la sortie du journal

La sortie du journal peut inclure un grand nombre d’informations. Cette commande répertorie tous les changements d’attribution de rôle et de définition de rôle dans un abonnement au cours des sept derniers jours, et filtre la sortie :

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

Voici un exemple de sortie du journal filtrée lors de la création d’une attribution de rôle :

```azurepowershell
Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:42 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:41 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"{roleAssignmentId}","Properties":{"PrincipalId":"{principalId}","PrincipalType":"User","RoleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64","Scope":"/subscriptions/
                          {subscriptionId}/resourceGroups/example-group"}}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

```

Si vous utilisez un principal de service pour créer des attributions de rôles, la propriété Appelant est un ID d’objet principal de service. Vous pouvez utiliser [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) pour obtenir des informations sur le principal du service.

```Example
Caller                  : {objectId}
EventTimestamp          : 3/1/2021 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
```

## <a name="azure-cli"></a>Azure CLI

Pour afficher les journaux d’activité avec l’interface de ligne de commande Azure, utilisez la commande [az monitor activity-log list](/cli/azure/monitor/activity-log#az_monitor_activity_log_list).

Cette commande répertorie les journaux d’activité d’un groupe de ressources sur une période de sept jours à partir du 1er mars :

```azurecli
az monitor activity-log list --resource-group example-group --start-time 2021-03-01 --offset 7d
```

Cette commande répertorie les journaux d’activité d’un fournisseur de ressources Authorization sur une période de sept jours à partir du 1er mars :

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d
```

### <a name="filter-log-output"></a>Filtrer la sortie du journal

La sortie du journal peut inclure un grand nombre d’informations. Cette commande répertorie tous les changements d’attribution et de définition de rôle dans un abonnement sur une période de sept jours, et filtre la sortie :

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d --query '[].{authorization:authorization, caller:caller, eventTimestamp:eventTimestamp, properties:properties}'
```

Voici un exemple de sortie du journal filtrée lors de la création d’une attribution de rôle :

```azurecli
[
 {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:42.456241+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "serviceRequestId": "{serviceRequestId}",
      "statusCode": "Created"
    }
  },
  {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:41.126243+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "requestbody": "{\"Id\":\"{roleAssignmentId}\",\"Properties\":{\"PrincipalId\":\"{principalId}\",\"PrincipalType\":\"User\",\"RoleDefinitionId\":\"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64\",\"Scope\":\"/subscriptions/{subscriptionId}/resourceGroups/example-group\"}}"
    }
  }
]
```

## <a name="azure-monitor-logs"></a>Journaux d’activité Azure Monitor

Les [journaux Azure Monitor](../azure-monitor/logs/log-query-overview.md) sont un autre outil que vous pouvez utiliser pour collecter et analyser les changements RBAC Azure de toutes vos ressources Azure. Les journaux Azure Monitor possèdent les avantages suivants :

- Écriture de requêtes et d’une logique complexes
- Intégration aux alertes, à Power BI et à d’autres outils
- Enregistrement des données pour des périodes de rétention plus longues
- Références croisées à d’autres journaux d’activité, tels que ceux liés à la sécurité, aux machines virtuelles et aux journaux d’activité personnalisés

Voici les étapes de base pour bien démarrer :

1. [Créez un espace de travail Log Analytics](../azure-monitor/logs/quick-create-workspace.md).

1. [Configurez la solution Activity Log Analytics](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution) pour vos espaces de travail.

1. [Affichez les journaux d’activité](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution). Un moyen rapide d’accéder à la page Vue d’ensemble de la solution Activity Log Analytics consiste à cliquer sur l’option **Journaux**.

   ![Option journaux Azure Monitor dans le portail](./media/change-history-report/azure-log-analytics-option.png)

1. Si vous le souhaitez, vous pouvez utiliser [Azure Monitor Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) pour interroger et afficher les journaux. Pour plus d’informations, voir [Bien démarrer avec les requêtes de journal dans Azure Monitor](../azure-monitor/logs/get-started-queries.md).

Voici une requête qui retourne les nouvelles attributions de rôle organisées par fournisseur de ressources cible :

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Voici une requête qui retourne des changements d’attribution de rôle affichés dans un graphique :

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Journaux d’activité à l’aide du portail Analytique avancée : capture d’écran](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Étapes suivantes
* [Afficher les journaux d’activité pour surveiller les actions sur les ressources](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Surveiller l’activité d’abonnement avec le journal des activités Azure](../azure-monitor/essentials/platform-logs-overview.md)