---
title: Collecter des données de journal
titleSuffix: Azure Cognitive Search
description: Collectez et analysez des données de journal en activant un paramètre de diagnostic, puis utilisez le langage de requête Kusto pour explorer les résultats.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 86e869bc08552ea11728c508486a4784eccf4042
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462352"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Collecter et analyser des données de journal pour Recherche cognitive Azure

Les journaux de diagnostic ou opérationnels fournissent des informations sur les opérations détaillées de Recherche cognitive Azure et sont utiles pour surveiller les processus de charge de travail et de service. En interne, les journaux d’activité existent sur le back-end pendant une courte période, suffisante pour l’investigation et l’analyse si vous émettez un ticket de support. Toutefois, si vous souhaitez utiliser la direction automatique sur les données opérationnelles, vous devez configurer un paramètre de diagnostic pour spécifier l’emplacement où les informations de journalisation sont collectées.

La configuration de journaux d’activité est utile pour les diagnostics et la conservation de l’historique opérationnel. Après avoir activé la journalisation, vous pouvez exécuter des requêtes ou créer des rapports pour une analyse structurée.

Le tableau suivant énumère les options de collecte et de persistance des données.

| Ressource | Utilisé pour |
|----------|----------|
| [Envoyer à l’espace de travail Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Les événements et les mesures sont envoyés à un espace de travail Log Analytics, lequel peut être interrogé dans le portail pour retourner des informations détaillées. Pour une introduction sur le sujet, consultez [Prise en main des journaux d’activité Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata). |
| [Archiver avec le stockage Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Les événements et les mesures sont archivés dans un conteneur de blobs et stockés dans des fichiers JSON. Les journaux d’activité peuvent être très granulaires (par heure/minute), ce qui est utile pour la recherche d’un incident spécifique, mais pas pour une investigation ouverte. Utilisez un éditeur JSON pour afficher un fichier journal brut ou Power BI pour agréger et visualiser les données du journal.|
| [Diffuser vers un Event Hub](https://docs.microsoft.com/azure/event-hubs/) | Les événements et les mesures sont diffusés vers un service Azure Event Hubs. Choisissez cette option comme autre service de collecte de données pour les journaux d’activité très volumineux. |

Les journaux Azure Monitor et le Stockage Blob sont tous les deux disponibles sous la forme d’un service gratuit pour vous permettre de l’essayer sans frais pendant la durée de vie de votre abonnement Azure. L’inscription à Application Insights et son utilisation sont gratuits tant que la taille de données d’application n’excède pas certaines limites. (Pour plus d’informations, consultez la [page des tarifs](https://azure.microsoft.com/pricing/details/monitor/).)

## <a name="prerequisites"></a>Prérequis

Si vous utilisez Log Analytics ou Stockage Azure, vous pouvez créer des ressources à l’avance.

+ [Créer un espace de travail Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [Créez un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="enable-data-collection"></a>Activer la collecte des données

Les paramètres de diagnostic spécifient la façon dont les événements et les mesures journalisés sont collectés.

1. Sous **Supervision**, sélectionnez **Paramètres de diagnostic**.

   ![Paramètres de diagnostic](./media/search-monitor-usage/diagnostic-settings.png "Paramètres de diagnostic")

1. Sélectionnez **+ Ajouter un paramètre de diagnostic**.

1. Cochez **Log Analytics**, sélectionnez votre espace de travail, puis sélectionnez **OperationLogs** et **AllMetrics**.

   ![Configurer la collecte de données](./media/search-monitor-usage/configure-storage.png "Configurer la collecte de données")

1. Enregistrez le paramètre.

1. Une fois la journalisation activée, utilisez votre service de recherche pour commencer à générer des journaux d’activité et des mesures. Cela prendra du temps avant que les mesures et les événements journalisés ne deviennent disponibles.

Pour Log Analytics, il faudra plusieurs minutes avant que les données ne soient disponibles, après quoi vous pourrez exécuter des requêtes Kusto pour retourner des données. Pour plus d’informations, consultez [Surveiller les demandes de requête](search-monitor-logs.md).

Pour le Stockage Blob, une heure est nécessaire pour que les conteneurs s’y affichent. Il y a un seul objet blob par heure et par conteneur. Les conteneurs sont créés uniquement quand il existe une activité à journaliser ou à mesurer. Quand les données sont copiées dans un compte de stockage, les données sont au format JSON et sont placées dans deux conteneurs :

+ journaux d’activité-Insights-operationlogs : pour les journaux d’activité de trafic de recherche
+ mesures-Insights-pt1m : pour les mesures

## <a name="query-log-information"></a>Informations sur le journal des requêtes

Dans les journaux de diagnostic, deux tables contiennent des journaux d’activité et des mesures pour Recherche cognitive Azure : **AzureDiagnostics** et **AzureMetrics**.

1. Sous **Supervision**, sélectionnez **Journaux d’activité**.

1. Entrez **AzureMetrics** dans la fenêtre de requête. Exécutez cette requête simple pour vous familiariser avec les données collectées dans cette table. Faites défiler la table pour afficher les mesures et les valeurs. Notez le nombre d’enregistrements indiqué en haut et, si votre service a collecté des mesures pendant un certain temps, vous souhaiterez peut-être ajuster l’intervalle de temps pour obtenir un jeu de données gérable.

   ![Table AzureMetrics](./media/search-monitor-usage/azuremetrics-table.png "Table AzureMetrics")

1. Entrez la requête suivante pour retourner un jeu de résultats tabulaire.

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Répétez les étapes précédentes, en commençant par **AzureDiagnostics** pour retourner toutes les colonnes à titre d’information, suivies d’une requête plus sélective qui extrait des informations plus intéressantes.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![Table AzureDiagnostics](./media/search-monitor-usage/azurediagnostics-table.png "Table AzureDiagnostics")

## <a name="log-schema"></a>Schéma du journal

Les structures de données qui contiennent des données de journal Recherche cognitive Azure sont conformes au schéma ci-dessous. 

Pour le Stockage Blob, chaque blob a un objet racine appelé **records** contenant un tableau d’objets de journal. Chaque objet blob contient des enregistrements de toutes les opérations qui ont eu lieu au cours de la même heure.

Le tableau suivant est une liste partielle des champs communs à la journalisation des diagnostics.

| Nom | Type | Exemple | Notes |
| --- | --- | --- | --- |
| timeGenerated |DATETIME |"2018-12-07T00:00:43.6872559Z" |Horodatage de l’opération |
| resourceId |string |«/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE » |Votre ID de ressource |
| operationName |string |« Query.Search » |Nom de l’opération |
| operationVersion |string |"2019-05-06" |Version d’API utilisée |
| catégorie |string |« OperationLogs » |constant |
| resultType |string |« Success » |Valeurs possibles : Réussite ou Échec |
| resultSignature |int |200 |Code de résultat HTTP |
| durationMS |int |50 |Durée de l’opération en millisecondes |
| properties |object |consultez le tableau suivant |Objet contenant des données propres à l’opération |

### <a name="properties-schema"></a>Schéma de propriétés

Les propriétés ci-dessous sont spécifiques à Recherche cognitive Azure.

| Nom | Type | Exemple | Notes |
| --- | --- | --- | --- |
| Description_s |string |« GET /indexes(’content’)/docs » |Point de terminaison de l’opération |
| Documents_d |int |42 |Nombre de documents traités |
| IndexName_s |string |"test-index" |Nom de l’index associé à l’opération |
| Query_s |string |"?search=AzureSearch&$count=true&api-version=2019-05-06" |Paramètres de requête |

## <a name="metrics-schema"></a>Schéma de mesures

Les mesures sont capturées pour les demandes de requête et mesurées à des intervalles d’une minute. Chaque mesure expose des valeurs minimales, maximales et moyennes par minute. Pour plus d’informations, consultez [Surveiller les demandes de requête](search-monitor-queries.md).

| Nom | Type | Exemple | Notes |
| --- | --- | --- | --- |
| resourceId |string |«/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE » |votre ID de ressource |
| metricName |string |« Latency » |Nom de la mesure |
| time |DATETIME |"2018-12-07T00:00:43.6872559Z" |Horodatage de l’opération |
| average |int |64 |Valeur moyenne des échantillons bruts dans l’intervalle de temps de la mesure, en secondes ou en pourcentage, en fonction de la mesure. |
| minimum |int |37 |Valeur minimale des échantillons bruts dans l’intervalle de temps de la mesure, en secondes. |
| maximum |int |78 |Valeur maximale des échantillons bruts dans l’intervalle de temps de la mesure, en secondes.  |
| total |int |258 |Valeur totale des échantillons bruts dans l’intervalle de temps de la mesure, en secondes.  |
| count |int |4 |Nombre de mesures émises à partir d’un nœud dans le journal pendant l’intervalle d’une minute.  |
| timegrain |string |« PT1M » |Fragment de temps de la mesure au format ISO 8601. |

Les requêtes sont souvent exécutées en quelques millisecondes, par conséquent seules les requêtes mesurées en secondes, QPS par exemple, apparaissent dans les métriques.

Dans le cas de la mesure **Search Queries Per Second**, la valeur minimale correspondra à la valeur la plus faible des requêtes de recherche par seconde qui a été enregistrée pendant cette minute. Il en va de même pour la valeur maximale. La moyenne représentera l’agrégat de ces valeurs pour toute la minute. Par exemple, sur une minute, le schéma peut être le suivant : une seconde de charge élevée, qui représente votre valeur SearchQueriesPerSecond maximale, puis 58 secondes de charge moyenne, et enfin une seconde avec une seule requête, qui représente la valeur minimale.

Pour **Pourcentage de requêtes de recherche limitées**, les valeurs minimales, maximales, moyennes et totales seront identiques : il s’agit du pourcentage de requêtes de recherche qui ont été limitées, en fonction du nombre total de requêtes de recherche pendant une minute.

## <a name="view-raw-log-files"></a>Afficher les fichiers journaux bruts

Le Stockage Blob est utilisé pour l’archivage des fichiers journaux. Vous pouvez utiliser n’importe quel éditeur JSON pour voir le fichier journal. Si vous n’en avez pas, nous vous recommandons [Visual Studio Code](https://code.visualstudio.com/download).

1. Dans le portail Azure, ouvrez votre compte de stockage. 

2. Dans le volet de navigation de gauche, cliquez sur **Objets blob**. **insights-logs-operationlogs** et **insights-metrics-pt1m** doivent s’afficher. Ces conteneurs sont créés par Recherche cognitive Azure quand les données de journal sont exportées vers le stockage Blob.

3. Faites défiler l’arborescence des dossiers vers le bas jusqu’à atteindre le fichier .json.  Utilisez le menu contextuel pour télécharger le fichier.

Une fois le fichier téléchargé, ouvrez-le dans un éditeur JSON pour en voir le contenu.

## <a name="next-steps"></a>Étapes suivantes

Si ce n’est déjà fait, passez en revue les principes de base de la surveillance des services de recherche pour en savoir plus sur les différentes capacités de surveillance disponibles.

> [!div class="nextstepaction"]
> [Surveiller les opérations et l’activité dans Recherche cognitive Azure](search-monitor-usage.md)