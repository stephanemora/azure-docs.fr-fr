---
title: Superviser les opérations et l’activité
titleSuffix: Azure Cognitive Search
description: Activer la journalisation, obtenir des métriques d’activité des requêtes, l’utilisation des ressources et d’autres données système à partir d’un service Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 353e00f902a7314e5e5b7c8ee03e8b925a510b26
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462324"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Superviser les opérations et l’activité de Recherche cognitive Azure

Cet article présente la supervision au niveau du service (ressource), au niveau de la charge de travail (requêtes et indexation) et suggère un framework pour superviser l’accès des utilisateurs.

Dans tous les domaines, vous utiliserez une combinaison d’infrastructures intégrées et de services fondamentaux comme Azure Monitor ainsi que des API de service qui retournent des statistiques, des nombres et un état. La compréhension de la gamme de fonctionnalités peut vous aider à construire une boucle de rétroaction afin de pouvoir résoudre les problèmes au fur et à mesure de leur apparition.

## <a name="use-azure-monitor"></a>Utiliser Azure Monitor

De nombreux services, notamment Recherche cognitive Azure, tirent parti d’[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/) pour les alertes, les métriques et la journalisation des données de diagnostic. Pour Recherche cognitive Azure, l’infrastructure de supervision intégrée est principalement utilisée pour la supervision au niveau des ressources (intégrité du service) et la [supervision des requêtes](search-monitor-queries.md).

La capture d’écran suivante vous aide à trouver les fonctionnalités Azure Monitor dans le portail.

+ L’onglet **Supervision**, situé dans la page de présentation principale, affiche un aperçu rapide des métriques clés.
+ Le **journal d’activité**, juste en dessous de la vue d’ensemble, signale les actions au niveau des ressources : intégrité du service et notifications de demande de clé API.
+ **Supervision**, plus bas dans la liste, fournit des alertes configurables, des métriques et des journaux de diagnostic. Créez-les lorsque vous en avez besoin. Une fois les données collectées et stockées, vous pouvez interroger ou visualiser les informations pour obtenir des insights.

![Intégration d’Azure Monitor dans un service de recherche](./media/search-monitor-usage/azure-monitor-search.png
 "Intégration d’Azure Monitor dans un service de recherche")

### <a name="precision-of-reported-numbers"></a>Précision des nombres signalés

Les pages du portail sont actualisées à des intervalles de quelques minutes. Par conséquent, les nombres signalés dans le portail sont approximatifs et sont destinés à vous donner une idée générale de l’efficacité avec laquelle votre système traite les demandes. Les métriques réelles, telles que les requêtes par seconde, peuvent être supérieures ou inférieures au nombre indiqué dans la page.

## <a name="activity-logs-and-service-health"></a>Journaux d’activité et intégrité du service

Le [**journal d’activité**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) collecte des informations auprès d’Azure Resource Manager et signale les changements de l’intégrité du service. Vous pouvez superviser les conditions critiques, d’erreur et d’avertissement relatives à l’intégrité du service dans le journal d’activité.

Pour les tâches intégrées au service, telles que les requêtes, l’indexation ou la création d’objets, vous verrez des notifications d’informations génériques comme *Obtenir une clé d’administration* et *Obtenir des clés de requête* pour chaque demande, mais pas l’action spécifique elle-même. Pour plus d’informations de ce fragment, vous devez configurer la journalisation des diagnostics.

Vous pouvez accéder au **journal d’activité** à partir du volet de navigation de gauche, à partir de l’option Notifications dans la barre de commandes située en haut de la fenêtre ou à partir de la page **Diagnostiquer et résoudre les problèmes**.

## <a name="monitor-storage"></a>Superviser le stockage

Les pages à onglets intégrées à la page de présentation indiquent l’utilisation des ressources. Ces informations deviennent disponibles dès que vous commencez à utiliser le service, sans qu’aucune configuration ne soit nécessaire, et la page est actualisée à des intervalles de quelques minutes. 

Si vous finalisez des décisions concernant le [niveau à utiliser pour les charges de travail de production](search-sku-tier.md) ou la nécessité d’[ajuster le nombre de partitions et de réplicas actifs](search-capacity-planning.md), ces métriques peuvent vous aider dans vos décisions en vous montrant rapidement les ressources consommées et l’efficacité de la configuration actuelle pour gérer la charge existante.

Les alertes liées au stockage ne sont pas disponibles pour l’instant ; la consommation de stockage n’est pas agrégée ni consignée dans la table **AzureMetrics** dans Azure Monitor. Vous devez [générer une solution personnalisée](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-creating) qui émet des notifications liées aux ressources, où votre code recherche la taille de stockage et gère la réponse. Pour plus d’informations sur les métriques de stockage, consultez [GET Service Statistics](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response).

Pour la supervision visuelle dans le portail, l’onglet **Utilisation** affiche la disponibilité des ressources par rapport aux [limites](search-limits-quotas-capacity.md) actuelles imposées par le niveau de service. 

L’illustration suivante concerne le service gratuit, qui est limité à 3 objets de chaque type et à 50 Mo de stockage. Un service De base ou Standard a des limites plus élevées. De plus, si vous augmentez le nombre de partitions, le stockage maximal augmente proportionnellement.

![État d’utilisation par rapport aux limites du niveau](./media/search-monitor-usage/usage-tab.png
 "État d’utilisation par rapport aux limites du niveau")

## <a name="monitor-workloads"></a>Superviser les charges de travail

Les événements journalisés incluent ceux qui sont liés à l’indexation et aux requêtes. La table **AzureDiagnostics** dans Log Analytics collecte les données opérationnelles relatives aux requêtes et à l’indexation.

La plupart des données journalisées concernent les opérations en lecture seule. Pour les autres opérations de création, mise à jour et suppression qui ne sont pas capturées dans le journal, vous pouvez interroger le service de recherche pour obtenir des informations système.

| NomOpération | Description |
|---------------|-------------|
| ServiceStats | Cette opération est un appel de routine à l’API [GET Service Statistics](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics), appelée directement ou implicitement pour remplir une page de présentation du portail lors de son chargement ou de son actualisation. |
| Query.Search |  Demandes de requêtes par rapport à un index. Consultez [Superviser les requêtes](search-monitor-queries.md) pour plus d’informations sur les requêtes journalisées.|
| Indexing.Index  | Cette opération est un appel à [Ajout, mise à jour ou suppression de documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). |
| indexes.Prototype | Il s’agit d’un index créé par l’Assistant Importation de données. |
| Indexers.Create | Créez un indexeur de manière explicite ou implicite à l’aide de l’Assistant Importation de données. |
| Indexers.Get | Retourne le nom d’un indexeur chaque fois que l’indexeur est exécuté. |
| Indexers.Status | Retourne l’état d’un indexeur chaque fois que l’indexeur est exécuté. |
| DataSources.Get | Retourne le nom de la source de données chaque fois qu’un indexeur est exécuté.|
| Indexes.Get | Retourne le nom d’un index chaque fois qu’un indexeur est exécuté. |

### <a name="kusto-queries-about-workloads"></a>Requêtes Kusto sur les charges de travail

Si vous avez activé la journalisation, vous pouvez interroger **AzureDiagnostics** pour obtenir la liste des opérations exécutées sur votre service et à quel moment. Vous pouvez également mettre en corrélation l’activité pour examiner les changements de performances.

#### <a name="example-list-operations"></a>Exemple : Lister les opérations 

Retournez une liste d’opérations et un nombre de chacune d’elles.

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Exemple : Mettre en corrélation les opérations

Associez une demande de requête à des opérations d’indexation et restituez les points de données sur un graphique de temps pour voir si les opérations coïncident.

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>Utiliser les API de recherche

L’API REST de Recherche cognitive Azure et le kit de développement logiciel (SDK) .NET fournissent tous les deux un accès programmatique aux métriques de service, aux informations sur les index et l’indexeur ainsi qu’aux décomptes de documents.

+ [GET Service Statistics](/rest/api/searchservice/get-service-statistics)
+ [GET Index Statistics](/rest/api/searchservice/get-index-statistics)
+ [GET Document Counts](/rest/api/searchservice/count-documents)
+ [GET Indexer Status](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>Superviser l’accès des utilisateurs

Étant donné que les index de recherche sont un composant d’une plus grande application cliente, il n’existe aucune méthodologie intégrée pour contrôler ni superviser l’accès par utilisateur à un index. Les demandes sont supposées provenir d’une application cliente, que ce soit pour des demandes d’administration ou de requête. Les opérations de lecture-écriture d’administration incluent la création, la mise à jour et la suppression d’objets dans l’ensemble du service. Les opérations en lecture seule sont des requêtes sur la collection de documents, délimitées à un seul index. 

Par conséquent, ce que vous verrez dans les journaux d’activité sont des références à des appels avec des clés d’administration ou des clés de requête. La clé appropriée est incluse dans les demandes provenant du code client. Le service n’est pas équipé pour gérer les jetons d’identité ni l’emprunt d’identité.

Quand il existe des exigences commerciales pour l’autorisation par utilisateur, la recommandation est l’intégration à Azure Active Directory. Vous pouvez utiliser $filter et des identités utilisateur pour [filtrer les résultats de recherche](search-security-trimming-for-azure-search-with-aad.md) des documents qu’un utilisateur ne doit pas voir. 

Il n’existe aucun moyen de consigner ces informations séparément de la chaîne de requête qui inclut le paramètre $filter. Consultez [Superviser les requêtes](search-monitor-queries.md) pour plus d’informations sur la création de rapports sur les chaînes de requête.

## <a name="next-steps"></a>Étapes suivantes

Une bonne maîtrise d’Azure Monitor est essentielle pour la surveillance de tout service Azure, dont des ressources comme Recherche cognitive Azure. Si vous n’êtes pas familiarisé avec Azure Monitor, prenez le temps de consulter les articles relatifs aux ressources. En plus des tutoriels, l’article suivant est un bon point de départ.

> [!div class="nextstepaction"]
> [Supervision de ressources Azure avec Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
