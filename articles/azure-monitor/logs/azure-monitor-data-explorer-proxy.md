---
title: Requête inter-ressources Azure Data Explorer à l’aide d’Azure Monitor
description: Utilisez Azure Monitor pour effectuer des requêtes inter-produits entre Azure Data Explorer, des espaces de travail Log Analytics et des applications Application Insights classiques dans Azure Monitor.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 8bc47eb2a6a22224f4df186136da2c184979ea24
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100599856"
---
# <a name="cross-resource-query-azure-data-explorer-by-using-azure-monitor"></a>Requête inter-ressources Azure Data Explorer à l’aide d’Azure Monitor
Azure Monitor prend en charge les requêtes inter-services entre Azure Data Explorer, [Application Insights](../app/app-insights-overview.md) et [Log Analytics](../logs/data-platform-logs.md). Vous pouvez ensuite interroger votre cluster Azure Data Explorer à l'aide des outils Log Analytics/Application Insights et y faire référence dans une requête inter-services. L’article montre comment effectuer une requête inter-services.

Le diagramme suivant illustre le flux inter-services Azure Monitor :

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Diagramme qui montre le flux des requêtes entre un utilisateur, Azure Monitor, un proxy et Azure Data Explorer.":::

>[!NOTE]
> La requête inter-services d’Azure Monitor est disponible en préversion publique. Pour toute question, contactez [l’équipe du service](mailto:ADXProxy@microsoft.com).

## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Interrogation croisée de vos ressources Log Analytics ou Application Insights et d’Azure Data Explorer

Vous pouvez exécuter des requêtes inter-ressources à l’aide des outils clients qui prennent en charge les requêtes Kusto. Les exemples de ces outils incluent l’interface utilisateur web Log Analytics, les classeurs, PowerShell et l’API REST.

Entrez l’identificateur d’un cluster Azure Data Explorer dans une requête au sein du modèle `adx` suivi du nom et de la table de la base de données.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Capture d’écran montrant un exemple de requête inter-services.":::

> [!NOTE]
>* Les noms de base de données respectent la casse.
>* La requête inter-ressources en tant qu’alerte n’est pas prise en charge.

## <a name="combine-azure-data-explorer-cluster-tables-with-a-log-analytics-workspace"></a>Combiner des tables de cluster Azure Data Explorer avec un espace de travail Log Analytics

Utilisez la commande `union` pour combiner des tables de cluster avec un espace de travail Log Analytics.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Capture d’écran qui montre un exemple de requête entre services avec la commande Union.":::

> [!Tip]
> Le format de raccourci est autorisé : *ClusterName*/*InitialCatalog*. Par exemple, `adx('help/Samples')` est translaté par `adx('help.kusto.windows.net/Samples')`.

## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Joindre les données d’un cluster Azure Data Explorer dans un locataire avec une ressource Azure Monitor située dans un autre

Les requêtes interlocataires entre les services ne sont pas prises en charge. Vous êtes connecté à un seul abonné pour exécuter la requête qui couvre les deux ressources.

Si la ressource Azure Data Explorer figure dans l’abonné A et que l’espace de travail Log Analytics figure dans l’abonné B, utilisez l’une des deux méthodes suivantes :

*  Azure Data Explorer vous permet d’ajouter des rôles pour les principaux dans différents locataires. Ajoutez votre ID d’utilisateur dans l’abonné B en tant qu’utilisateur autorisé sur le cluster Azure Data Explorer. Validez le fait que la propriété [TrustedExternalTenant](/powershell/module/az.kusto/update-azkustocluster) sur le cluster Azure Data Explorer contient l’abonné B. Exécutez la requête croisée entièrement dans l’abonné B.
*  Utilisez [Lighthouse](../../lighthouse/index.yml) pour projeter la ressource Azure Monitor dans l’abonné A.

## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Se connecter à des clusters Azure Data Explorer à partir de différents locataires

Kusto Explorer vous connecte automatiquement à l’abonné auquel le compte d’utilisateur appartient à l’origine. Pour accéder aux ressources d’autres abonnés avec le même compte d’utilisateur, vous devez spécifier `TenantId` de façon explicite dans la chaîne de connexion :

`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=TenantId`

## <a name="next-steps"></a>Étapes suivantes
* [Écrire des requêtes](/azure/data-explorer/write-queries)
* [Interroger des données dans Azure Monitor à l’aide d’Azure Data Explorer](/azure/data-explorer/query-monitor-data)
* [Effectuer des requêtes de journal inter-ressources dans Azure Monitor](../logs/cross-workspace-query.md)