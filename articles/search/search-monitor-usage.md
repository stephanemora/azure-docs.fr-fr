---
title: Superviser l’utilisation des ressources et les métriques des requêtes
titleSuffix: Azure Cognitive Search
description: Activer la journalisation, obtenir des métriques d’activité des requêtes, l’utilisation des ressources et d’autres données système à partir d’un service Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7ef868f156ac537cb066f293872f69135c4df25f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77059644"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-cognitive-search"></a>Superviser la consommation des ressources et l’activité des requêtes dans Recherche cognitive Azure

Dans la page Vue d’ensemble de votre service Recherche cognitive Azure, vous pouvez voir les données système relatives à l’utilisation des ressources, aux métriques des requêtes et au quota disponible pour créer des index, des indexeurs et des sources de données supplémentaires. Vous pouvez également utiliser le portail pour configurer l’analytique des journaux d’activité ou une autre ressource utilisée pour la collecte des données persistantes. 

La configuration de journaux d’activité est utile pour les autodiagnostics et la conservation de l’historique opérationnel. En interne, les journaux d’activité existent sur le back-end pendant une courte période, suffisante pour l’investigation et l’analyse si vous émettez un ticket de support. Si vous voulez contrôler les informations des journaux et y accéder, vous devez configurer l’une des solutions décrites dans cet article.

Dans cet article, découvrez vos options de supervision, comment activer la journalisation et le stockage des journaux, et comment voir le contenu des journaux.

## <a name="metrics-at-a-glance"></a>Aperçu des métriques

Les sections **Utilisation** et **Supervision** intégrées à la page Vue d’ensemble exposent la consommation des ressources et les mesures d’exécution des requêtes. Ces informations deviennent disponibles dès que vous commencez à utiliser le service, sans qu’aucune configuration ne soit nécessaire. Cette page est actualisée à des intervalles de quelques minutes. Si vous finalisez des décisions concernant le [niveau à utiliser pour les charges de travail de production](search-sku-tier.md) ou la nécessité d’[ajuster le nombre de partitions et de réplicas actifs](search-capacity-planning.md), ces métriques peuvent vous aider dans vos décisions en vous montrant rapidement les ressources consommées et l’efficacité de la configuration actuelle pour gérer la charge existante.

L’onglet **Utilisation** affiche la disponibilité des ressources par rapport aux [limites](search-limits-quotas-capacity.md) actuelles. L’illustration suivante concerne le service gratuit, qui est limité à 3 objets de chaque type et à 50 Mo de stockage. Un service De base ou Standard a des limites plus élevées. De plus, si vous augmentez le nombre de partitions, le stockage maximal augmente proportionnellement.

![État d’utilisation par rapport aux limites effectives](./media/search-monitor-usage/usage-tab.png
 "État d’utilisation par rapport aux limites effectives")

## <a name="queries-per-second-qps-and-other-metrics"></a>Nombre de requêtes par seconde (RPS) et autres métriques

L’onglet **Supervision** affiche les moyennes mobiles pour des métriques telles que la recherche du *Nombre de requêtes par seconde* (QPS), agrégées par minute. 
La *Latence de recherche* est la durée nécessaire au service de recherche pour traiter les requêtes de recherche, agrégées par minute. Le *Pourcentage de requêtes de recherche limitées* (non affiché) est le pourcentage de requêtes de recherche qui ont été limitées, également agrégées par minute.

Ces valeurs sont approximatives et sont destinées à vous donner une idée générale de l’efficacité avec laquelle votre système traite les requêtes. Le nombre de requêtes par seconde réel peut être supérieur ou inférieur au nombre indiqué dans le portail.

![Requêtes par seconde d’activité](./media/search-monitor-usage/monitoring-tab.png "Requêtes par seconde d’activité")

## <a name="activity-logs"></a>Journaux d’activité

Le **journal d’activité** collecte des informations à partir d’Azure Resource Manager. La création ou la suppression d’un service, la mise à jour d’un groupe de ressources, la vérification de la disponibilité d’un nom ou l’obtention d’une clé d’accès au service pour traiter une requête sont des exemples d’informations figurant dans le journal d’activité. 

Vous pouvez accéder au **journal d’activité** à partir du volet de navigation de gauche, à partir de l’option Notifications dans la barre de commandes située en haut de la fenêtre ou à partir de la page **Diagnostiquer et résoudre les problèmes**.

Pour les tâches intégrées au service, comme la création d’un index ou la suppression d’une source de données, vous verrez des notifications génériques comme « Obtenir une clé d’administration » pour chaque requête, mais pas l’action spécifique proprement dite. Pour ce niveau d’informations, vous devez activer une solution de supervision de module complémentaire.

## <a name="add-on-monitoring-solutions"></a>Solutions de supervision de module complémentaire

Recherche cognitive Azure ne stocke pas de données qui ne figurent pas dans les objets qu’elle gère, ce qui signifie que les données de journal doivent être stockées en externe. Vous pouvez configurer n’importe laquelle des ressources ci-dessous si vous voulez conserver les données de journal. 

Le tableau suivant compare les options de stockage des journaux d’activité et d’ajout d’une supervision des opérations de service et des charges de travail de requêtes par le biais d’Application Insights.

| Ressource | Utilisé pour |
|----------|----------|
| [Journaux d’activité Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | Événements et métriques des requêtes journalisés, basés sur l’un des schémas ci-dessous. Les événements sont journalisés dans un espace de travail Log Analytics. Vous pouvez exécuter des requêtes sur un espace de travail pour retourner des informations détaillées du journal. Pour plus d’informations, consultez [Bien démarrer avec les journaux Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Stockage Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Événements et métriques des requêtes journalisés, basés sur l’un des schémas ci-dessous. Les événements sont journalisés dans un conteneur d’objets blob et stockés dans des fichiers JSON. Utilisez un éditeur JSON pour afficher le contenu des fichiers.|
| [Concentrateur d’événements](https://docs.microsoft.com/azure/event-hubs/) | Événements et métriques des requêtes journalisés, basés sur les schémas présentés dans cet article. Choisissez cette option comme autre service de collecte de données pour les journaux d’activité très volumineux. |

Les journaux Azure Monitor et le Stockage Blob sont tous les deux disponibles sous la forme d’un service gratuit pour vous permettre de l’essayer sans frais pendant la durée de vie de votre abonnement Azure. L’inscription à Application Insights et son utilisation sont gratuits tant que la taille de données d’application n’excède pas certaines limites. (Pour plus d’informations, consultez la [page des tarifs](https://azure.microsoft.com/pricing/details/monitor/).)

La section suivante vous guide tout au long des étapes d’activation et d’utilisation du Stockage Blob Azure pour collecter des données de journal créées par les opérations Recherche cognitive Azure et y accéder.

## <a name="enable-logging"></a>Activation de la journalisation

La journalisation pour l’indexation et les charges de travail de requêtes est désactivée par défaut et dépend des solutions de module complémentaire pour l’infrastructure de journalisation et le stockage externe à long terme. En soi, les seules données persistantes dans Recherche cognitive Azure sont les objets qu’elle crée et gère. Les journaux d’activité doivent donc être stockés ailleurs.

Dans cette section, vous allez apprendre à utiliser le Stockage Blob pour stocker des événements journalisés et des données de métriques.

1. [Créez un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) si vous n’en avez pas déjà un. Vous pouvez le placer dans le même groupe de ressources que la Recherche cognitive Azure pour simplifier le nettoyage ultérieur si vous voulez supprimer toutes les ressources utilisées dans cet exercice.

   Votre compte de stockage doit exister dans la même région que Recherche cognitive Azure.

2. Ouvrez la page Vue d’ensemble de votre service de recherche. Dans le volet de navigation de gauche, faites défiler l’écran vers le bas jusqu’à **Supervision**, puis cliquez sur **Paramètres de diagnostic**.

   ![Paramètres de diagnostic](./media/search-monitor-usage/diagnostic-settings.png "Paramètres de diagnostic")

3. Sélectionnez **Ajouter un paramètre de diagnostic**.

4. Choisissez les données à exporter : Journaux d’activité, Métriques ou les deux. Vous pouvez les copier sur un compte de stockage, les envoyer à un Event Hub ou les exporter vers les journaux Azure Monitor.

   Pour l’archivage dans le Stockage Blob, seul le compte de stockage doit exister. Les conteneurs et les objets blob sont créés au besoin lors de l’exportation des données de journal.

   ![Configurer l’archive de stockage Blob](./media/search-monitor-usage/configure-blob-storage-archive.png "Configurer l’archive de stockage Blob")

5. Enregistrer le profil

6. Testez la journalisation en créant ou en supprimant des objets (ce qui crée des événements de journal) et en soumettant des requêtes (ce qui génère des métriques). 

La journalisation est activée une fois que vous enregistrez le profil. Les conteneurs sont créés uniquement quand il existe une activité à journaliser ou à mesurer. Quand les données sont copiées dans un compte de stockage, les données sont au format JSON et sont placées dans deux conteneurs :

* journaux d’activité-Insights-operationlogs : pour les journaux d’activité de trafic de recherche
* mesures-Insights-pt1m : pour les mesures

**Une heure est nécessaire pour que les conteneurs s’affichent dans le Stockage Blob. Il y a un seul objet blob par heure et par conteneur.**

Vous pouvez utiliser [Visual Studio Code](#download-and-open-in-visual-studio-code) ou un autre éditeur JSON pour afficher les fichiers. 

### <a name="example-path"></a>Exemple de chemin

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>Schéma du journal
Les objets blob contenant vos journaux d’activité du trafic de votre service de recherche sont structurés comme décrit dans cette section. Chaque objet blob a un objet racine appelé **records** contenant un tableau d’objets de journal. Chaque objet blob contient des enregistrements de toutes les opérations qui ont eu lieu au cours de la même heure.

| Name | Type | Exemple | Notes |
| --- | --- | --- | --- |
| time |DATETIME |"2018-12-07T00:00:43.6872559Z" |Horodatage de l’opération |
| resourceId |string |«/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE » |Votre ID de ressource |
| operationName |string |« Query.Search » |Nom de l’opération |
| operationVersion |string |"2019-05-06" |Version d’API utilisée |
| catégorie |string |« OperationLogs » |constant |
| resultType |string |« Success » |Valeurs possibles : Réussite ou Échec |
| resultSignature |int |200 |Code de résultat HTTP |
| durationMS |int |50 |Durée de l’opération en millisecondes |
| properties |object |consultez le tableau suivant |Objet contenant des données propres à l’opération |

**Schéma de propriétés**

| Name | Type | Exemple | Notes |
| --- | --- | --- | --- |
| Description |string |« GET /indexes(’content’)/docs » |Point de terminaison de l’opération |
| Requête |string |"?search=AzureSearch&$count=true&api-version=2019-05-06" |Paramètres de requête |
| Documents |int |42 |Nombre de documents traités |
| IndexName |string |« testindex » |Nom de l’index associé à l’opération |

## <a name="metrics-schema"></a>Schéma de mesures

Les métriques sont capturées pour les demandes de requête.

| Name | Type | Exemple | Notes |
| --- | --- | --- | --- |
| resourceId |string |«/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE » |votre ID de ressource |
| metricName |string |« Latency » |Nom de la mesure |
| time |DATETIME |"2018-12-07T00:00:43.6872559Z" |Horodatage de l’opération |
| average |int |64 |Valeur moyenne des échantillons bruts dans l’intervalle de temps de la mesure |
| minimum |int |37 |Valeur minimale des échantillons bruts dans l’intervalle de temps de la mesure |
| maximum |int |78 |Valeur maximale des échantillons bruts dans l’intervalle de temps de la mesure |
| total |int |258 |Valeur totale des échantillons bruts dans l’intervalle de temps de la mesure |
| count |int |4 |Nombre d’échantillons bruts utilisés pour générer la mesure |
| timegrain |string |« PT1M » |Fragment de temps de la mesure au format ISO 8601 |

Toutes les mesures sont consignées dans des intervalles d’une minute. Chaque mesure expose des valeurs minimales, maximales et moyennes par minute.

Dans le cas de la mesure SearchQueriesPerSecond, la valeur minimale correspondra à la valeur la plus faible des requêtes de recherche par seconde qui a été enregistrée pendant cette minute. Il en va de même pour la valeur maximale. La moyenne représentera l’agrégat de ces valeurs pour toute la minute.
Imaginez ce scénario : pendant une minute, vous pouvez avoir 58 secondes de charge très élevée, qui représentera votre valeur SearchQueriesPerSecond maximale, puis 58 secondes de charge moyenne, et enfin une seconde avec une seule requête, qui représentera la valeur minimale.

Pour ThrottledSearchQueriesPercentage, les valeurs minimales, maximales, moyennes et totales seront identiques : il s’agit du pourcentage de requêtes de recherche qui ont été limitées, en fonction du nombre total de requêtes de recherche pendant une minute.

## <a name="download-and-open-in-visual-studio-code"></a>Télécharger et ouvrir dans Visual Studio Code

Vous pouvez utiliser n’importe quel éditeur JSON pour voir le fichier journal. Si vous n’en avez pas, nous vous recommandons [Visual Studio Code](https://code.visualstudio.com/download).

1. Dans le portail Azure, ouvrez votre compte de stockage. 

2. Dans le volet de navigation de gauche, cliquez sur **Objets blob**. **insights-logs-operationlogs** et **insights-metrics-pt1m** doivent s’afficher. Ces conteneurs sont créés par Recherche cognitive Azure quand les données de journal sont exportées vers le stockage Blob.

3. Faites défiler l’arborescence des dossiers vers le bas jusqu’à atteindre le fichier .json.  Utilisez le menu contextuel pour télécharger le fichier.

Une fois le fichier téléchargé, ouvrez-le dans un éditeur JSON pour en voir le contenu.

## <a name="use-system-apis"></a>Utiliser des API système
L’API REST de Recherche cognitive Azure et le kit de développement logiciel (SDK) .NET fournissent tous les deux un accès programmatique aux métriques de service, aux informations sur les index et l’indexeur ainsi qu’aux décomptes de documents.

* [Obtenir les statistiques des services](/rest/api/searchservice/get-service-statistics)
* [Obtention de statistiques d’index](/rest/api/searchservice/get-index-statistics)
* [Nombre de documents](/rest/api/searchservice/count-documents)
* [Obtention de l’état d’indexeur](/rest/api/searchservice/get-indexer-status)

Pour activer à l’aide de PowerShell ou de l’interface de ligne de commande Azure, consultez la documentation qui se trouve [ici](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="next-steps"></a>Étapes suivantes

[Gérer votre service Recherche sur Microsoft Azure](search-manage.md) pour plus d’informations sur l’administration des services. [Performances et optimisation](search-performance-optimization.md) pour obtenir de l’aide sur le paramétrage.
