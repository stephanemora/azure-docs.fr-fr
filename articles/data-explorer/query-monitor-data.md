---
title: Interroger des données dans Azure Monitor avec Azure Data Explorer (préversion)
description: Dans cette rubrique, vous interrogez les données d’Azure Monitor en créant un proxy Azure Data Explorer pour les requêtes de produit croisé avec Application Insights et Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 8e61f52282bcbc62a3eb069272cd7c1f3e329d3b
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172706"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Interroger des données dans Azure Monitor avec Azure Data Explorer (préversion)

Le cluster proxy Azure Data Explorer (ADX Proxy) est une entité qui vous permet d’effectuer des requêtes entre Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview) et [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) dans le service [Azure Monitor](/azure/azure-monitor/). Vous pouvez mapper les espaces de travail d’Azure Monitor Log Analytics ou les applications d’Application Insights comme un cluster proxy. Vous pouvez ensuite interroger le cluster proxy à l’aide des outils Azure Data Explorer et vous y référer dans une requête entre clusters. L’article montre comment se connecter à un cluster proxy, ajouter un cluster proxy à l’interface utilisateur web d’Azure Data Explorer, et exécuter des requêtes contre vos applications AI ou vos espaces de travail LA depuis Azure Data Explorer.

Le flux de proxy Azure Data Explorer : 

![Flux de proxy ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Prérequis

> [!NOTE]
> Le proxy ADX est en mode aperçu. Pour activer cette fonctionnalité, contactez l’équipe [ADXProxy](mailto:adxproxy@microsoft.com).

## <a name="connect-to-the-proxy"></a>Se connecter au proxy

1. Vérifiez que votre cluster natif Azure Data Explorer (comme le cluster *d’aide*) apparaît dans le menu de gauche avant de vous connecter à votre cluster Log Analytics ou Application Insights.

    ![Cluster natif ADX](media/adx-proxy/web-ui-help-cluster.png)

1. Dans l’interface utilisateur d’Azure Data Explorer https://dataexplorer.azure.com/clusters), sélectionnez **Ajouter un cluster**.

1. Dans la fenêtre **Ajouter un cluster** :

    * Ajoutez l’URL menant au cluster LA ou AI. Par exemple : `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * Sélectionnez **Ajouter**.

    ![Ajouter un cluster](media/adx-proxy/add-cluster.png)

    Si vous ajoutez une connexion à plusieurs clusters proxy, attribuez un nom différent à chacun d’eux. Sinon ils auront tous le même nom dans le volet gauche.

1. Une fois la connexion établie, votre cluster LA ou AI apparaît dans le volet gauche avec votre cluster natif ADX. 

    ![Clusters Log Analytics et Azure Data Explorer](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Exécuter des requêtes

Vous pouvez utiliser Kusto Explorer, ADX Web Explorer, Jupyter Kqlmagic ou l’API REST pour interroger les clusters proxy. 

> [!TIP]
> * Le nom de la base de données doit avoir le même nom que la ressource spécifiée dans le cluster proxy. Les noms respectent la casse.
> * Dans les requêtes entre clusters, assurez-vous que les noms des applications Application Insights et des espaces de travail Log Analytics sont corrects.
>     * Si les noms contiennent des caractères spéciaux, ils sont remplacés par l’encodage d’URL dans le nom du cluster proxy. 
>     * Si les noms contiennent des caractères qui ne respectent pas les [règles de nom d’identificateur KQL](/azure/kusto/query/schema-entities/entity-names), ils sont remplacés par le caractère tiret **-** .

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>Requête contre le cluster natif Azure Data Explorer 

Exécutez des requêtes sur votre cluster Azure Data Explorer (comme la table *StormEvents* dans le cluster *d’aide*). Lors de l’exécution de la requête, vérifiez que votre cluster natif Azure Data Explorer est sélectionné dans le volet gauche.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![Requête de la table StormEvents](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>Requête contre votre cluster LA ou AI

Lorsque vous exécutez des requêtes sur votre cluster LA ou AL, vérifiez que votre cluster LA ou AI est sélectionné dans le volet gauche. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Requête de l’espace de travail LA](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>Interrogez votre cluster LA ou AI à partir du proxy ADX  

Lorsque vous lancez des requêtes sur votre cluster LA ou AI à partir du proxy, vérifiez que votre cluster natif ADX est sélectionné dans le volet de gauche. L’exemple suivant illustre une requête de l’espace de travail LA à l’aide du cluster natif ADX

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Requête à partir du proxy Azure Data Explorer](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>Requête croisée du cluster LA ou AI et du cluster ADX depuis le proxy ADX 

Lorsque vous lancez des requêtes croisées à partir du proxy, vérifiez que votre cluster natif ADX est sélectionné dans le volet de gauche. Les exemples suivants illustrent la combinaison de tables de cluster ADX (à l’aide de `union`) avec l’espace de travail LA.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Requête croisée à partir du proxy Azure Data Explorer](media/adx-proxy/cross-query-adx-proxy.png)

L’utilisation de l’[`join`opérateur](/azure/kusto/query/joinoperator), au lieu de l’union, peut nécessiter un conseil pour l’exécuter sur un cluster natif Azure Data Explorer (et non sur le proxy). 

## <a name="additional-syntax-examples"></a>Exemples de syntaxe supplémentaire

Les options de syntaxe suivantes sont disponibles lors de l’appel des clusters Application Insights (IA) ou Log Analytics (LA) :

|Description de la syntaxe  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Base de données dans un cluster qui contient uniquement la ressource définie dans cet abonnement (**recommandé pour les requêtes entre clusters**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster contenant l’ensemble des applications/espaces de travail de cet abonnement    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster qui contient l’ensemble des applications/espaces de travail de l’abonnement et qui sont membres de ce groupe de ressources    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster qui contient uniquement la ressource définie dans cet abonnement      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Étapes suivantes

[Écrire des requêtes](write-queries.md)