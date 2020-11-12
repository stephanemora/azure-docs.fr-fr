---
title: Interroger des données dans Azure Monitor avec Azure Data Explorer (préversion)
description: Utilisez Azure Data Explorer pour effectuer des requêtes interproduits entre Azure Data Explorer, des espaces de travail Log Analytics et des applications Application Insights classiques dans Azure Monitor.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 9b434c426264fcfee0dfe663a7d1b21a354badec
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491254"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Interroger des données dans Azure Monitor avec Azure Data Explorer (préversion)
Le cluster proxy Azure Data Explorer vous permet d’effectuer des requêtes interproduits entre Azure Data Explorer, des espaces de travail Log Analytics et des applications Application Insights classiques dans Azure Monitor. Vous pouvez mapper les espaces de travail Log Analytics dans Azure Monitor ou les applications Application Insights comme clusters proxy. Vous pouvez ensuite interroger le cluster proxy à l’aide des outils Azure Data Explorer et vous y référer dans une requête entre clusters. Cet article montre comment se connecter à un cluster proxy, ajouter un cluster proxy à l’interface utilisateur web d’Azure Data Explorer et exécuter des requêtes sur vos espaces de travail Log Analytics ou vos applications Application Insights depuis Azure Data Explorer.

Le diagramme suivant illustre le flux de proxy Azure Data Explorer :

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-monitor-proxy-flow.png" alt-text="Flux de proxy Azure Data Explorer.":::


> [!NOTE]
> Le proxy Azure Data Explorer est en préversion publique. [Connectez-vous au proxy](#connect-to-the-proxy) pour activer la fonctionnalité de proxy pour vos clusters. 

## <a name="connect-to-the-proxy"></a>Se connecter au proxy
Pour connecter votre espace de travail Log Analytics ou votre application Application Insights classique, ouvrez l’[interface utilisateur web d’Azure Data Explorer](https://dataexplorer.azure.com/clusters). Vérifiez que votre cluster natif Azure Data Explorer (comme le cluster *d’aide* ) apparaît dans le menu de gauche avant de vous connecter à votre cluster Log Analytics ou Application Insights.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Cluster natif Azure Data Explorer.":::

Cliquez sur **Ajouter un cluster** , puis ajoutez l’URL du cluster Log Analytics ou Application Insights dans l’un des formats suivants. 
    
* Pour Log Analytics : `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
* Pour Application Insights : `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

Cliquez sur **Ajouter** pour établir la connexion.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Ajouter le cluster.":::
 
> [!NOTE]
> Si vous ajoutez une connexion à plusieurs clusters proxy, attribuez un nom différent à chacun d’eux. Sinon ils auront tous le même nom dans le volet gauche.

Une fois la connexion établie, votre cluster Log Analytics ou Application Insights s’affiche dans le volet gauche avec votre cluster Azure Data Explorer natif. 

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Clusters Log Analytics et Azure Data Explorer.":::
 
> [!NOTE]
> Le nombre d’espaces de travail Azure Monitor pouvant être mappés est limité à 100.

## <a name="create-queries-using-azure-monitor-data"></a>Créer des requêtes à l’aide des données Azure Monitor

Vous pouvez exécuter les requêtes à l’aide des outils clients qui prennent en charge les requêtes Kusto, par exemple : Kusto Explorer, l’interface utilisateur web d’Azure Data Explorer, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, Lens et API REST.

> [!NOTE]
> La fonctionnalité de proxy Azure Data Explorer est utilisée uniquement pour l’extraction de données. Pour plus d’informations, consultez [Prise en charge des fonctions](#function-supportability).

> [!TIP]
> * Le nom de la base de données doit avoir le même nom que la ressource spécifiée dans le cluster proxy. Les noms respectent la casse.
> * Dans les requêtes entre clusters, assurez-vous que les noms des applications Application Insights et des espaces de travail Log Analytics sont corrects.
>     * Si les noms contiennent des caractères spéciaux, ils sont remplacés par l’encodage d’URL dans le nom du cluster proxy. 
>     * Si les noms contiennent des caractères qui ne respectent pas les [règles de nom d’identificateur KQL](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/entity-names), ils sont remplacés par le caractère tiret **-** .

### <a name="direct-query-from-your-log-analytics-or-application-insights-proxy-cluster"></a>Requête directe à partir de votre cluster proxy Log Analytics ou Application Insights

Exécutez des requêtes sur votre cluster Log Analytics ou Application Insights. Vérifiez que votre cluster est sélectionné dans le volet gauche. 
 
```kusto
Perf | take 10 // Demonstrate query through the proxy on the Log Analaytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Interrogez l’espace de travail Log Analytics.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-proxy-cluster-and-the-azure-data-explorer-native-cluster"></a>Requête croisée de votre cluster proxy Log Analytics ou Application Insights et du cluster natif Azure Data Explorer

Lorsque vous exécutez des requêtes interclusters à partir du proxy, vérifiez que votre cluster natif Azure Data Explorer est sélectionné dans le volet de gauche. Les exemples suivants illustrent la combinaison de tables de cluster Azure Data Explorer à l’aide de l’opérateur [union](/azure/data-explorer/kusto/query/unionoperator) avec un espace de travail Log Analytics.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```
L’utilisation de l’[opérateur `join`](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor), au lieu d’union, peut nécessiter un [indicateur](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor#join-hints) pour son exécution sur un cluster natif Azure Data Explorer (et pas sur le proxy). 

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Joindre les données d’un cluster Azure Data Explorer dans un locataire avec une ressource Azure Monitor située dans un autre

Les requêtes multilocataires ne sont pas prises en charge par le proxy Azure Data Explorer. Vous êtes connecté à un seul locataire pour exécuter la requête qui couvre les deux ressources.

Si la ressource Azure Data Explorer figure dans le locataire « A » et que l’espace de travail Log Analytics figure dans le locataire « B », utilisez l’une des deux méthodes suivantes :

- Azure Data Explorer vous permet d’ajouter des rôles pour les principaux dans différents locataires. Ajoutez votre ID d’utilisateur dans le locataire « B » en tant qu’utilisateur autorisé sur le cluster Azure Data Explorer. Validez la propriété *['TrustedExternalTenant'](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* sur le cluster Azure Data Explorer qui contient le locataire « B ». Exécutez la requête croisée intégralement dans le locataire « B ».

- Utilisez [Lighthouse](/azure/lighthouse/) pour projeter la ressource Azure Monitor dans le locataire « A ».

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Se connecter à des clusters Azure Data Explorer à partir de différents locataires

Kusto Explorer vous connecte automatiquement au locataire auquel le compte d’utilisateur appartient à l’origine. Pour accéder aux ressources d’autres locataires avec le même compte d’utilisateur, le `tenantId` doit être spécifié explicitement dans la chaîne de connexion : `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`**TenantId**

## <a name="function-supportability"></a>Prise en charge des fonctions

Le cluster proxy Azure Data Explorer prend en charge des fonctions pour Log Analytics et Application Insights. Cela permet aux requêtes interclusters de référencer directement une fonction tabulaire Azure Monitor.

Les commandes suivantes sont prises en charge par le proxy :

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

L’image suivante illustre un exemple d’interrogation d’une fonction tabulaire à partir de l’interface utilisateur web d’Azure Data Explorer. Pour utiliser la fonction, exécutez son nom dans la fenêtre de requête.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Interrogez une fonction tabulaire à partir de l’interface utilisateur web d’Azure Data Explorer.":::
 
> [!NOTE]
> Azure Monitor prend en charge uniquement les fonctions tabulaires, qui ne prennent pas en charge les paramètres.

## <a name="additional-syntax-examples"></a>Exemples de syntaxe supplémentaire

Les options de syntaxe suivantes sont disponibles lors de l’appel des clusters Log Analytics ou Application Insights :

|Description de la syntaxe  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Base de données dans un cluster qui contient uniquement la ressource définie dans cet abonnement ( **recommandé pour les requêtes entre clusters** ) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster contenant l’ensemble des applications/espaces de travail de cet abonnement    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster qui contient l’ensemble des applications/espaces de travail de l’abonnement et qui sont membres de ce groupe de ressources    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster qui contient uniquement la ressource définie dans cet abonnement      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus d’informations sur la [structure de données des espaces de travail Log Analytics et d’Application Insights](data-platform-logs.md).
- Découvrez comment [écrire des requêtes dans Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/write-queries).