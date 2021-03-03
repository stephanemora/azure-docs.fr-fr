---
title: Azure Monitor pour Azure Data Explorer (préversion) | Microsoft Docs
description: Cet article décrit Azure Monitor Insights pour les clusters Azure Data Explorer.
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 6276527230cf889b471887fdb3da70e06a2fc8d6
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582352"
---
# <a name="azure-monitor-for-azure-data-explorer-preview"></a>Azure Monitor pour Azure Data Explorer (préversion)

Azure Monitor pour Azure Data Explorer (préversion) fournit une surveillance complète de vos clusters grâce à une vue unifiée des performances, des opérations, de l’utilisation et des défaillances de vos clusters.
Cet article vous aidera à comprendre comment intégrer et utiliser Azure Monitor pour Azure Data Explorer (préversion).

## <a name="introduction-to-azure-monitor-for-azure-data-explorer-preview"></a>Introduction à Azure Monitor pour Azure Data Explorer (préversion)

Avant de vous lancer dans cette expérience, vous devez comprendre comment cette fonctionnalité présente et affiche les informations.
-    **Perspective à grande l’échelle** offrant une vue instantanée des principales métriques de vos clusters, pour suivre facilement les performances des requêtes, l’ingestion et les opérations d’exportation.
-   **Analyse approfondie** d’un cluster Azure Data Explorer particulier pour faciliter l’analyse détaillée.
-    **Personnalisable** en vous permettant de modifier les métriques que vous souhaitez voir, modifier ou définir des seuils qui s’alignent sur vos limites et enregistrer vos propres classeurs personnalisés. Les graphiques du classeur peuvent être épinglés aux tableaux de bord Azure.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Affichage à partir d’Azure Monitor (perspective à grande échelle)

À partir d’Azure Monitor, vous pouvez afficher les principales métriques de performances du cluster, notamment les métriques des requêtes, l’ingestion et les opérations d’exportation à partir de plusieurs clusters de votre abonnement, et faciliter l’identification des problèmes de performance.

Pour afficher les performances de vos clusters sur l’ensemble de vos abonnements, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Sélectionnez **Surveillance** dans le volet gauche du portail Azure, puis dans la section Hub Insights, sélectionnez **Clusters Azure Data Explorer (préversion)** .

![Capture d’écran de l’expérience de vue d’ensemble avec plusieurs graphiques](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>Onglet Overview

Dans l’onglet **Vue d’ensemble** de l’abonnement sélectionné, le tableau affiche les métriques interactives des clusters Azure Data Explorer regroupés dans l’abonnement. Vous pouvez filtrer les résultats en fonction des options que vous sélectionnez dans les listes déroulantes suivantes :

* Abonnements : seuls les abonnements qui comportent des clusters Azure Data Explorer sont répertoriés.

* Clusters Azure Data Explorer : par défaut, jusqu’à cinq clusters maximum sont présélectionnés. Si vous sélectionnez tous les clusters ou plusieurs d’entre eux dans le sélecteur d’étendue, jusqu’à 200 clusters seront retournés.

* Intervalle de temps : par défaut, affiche les 24 dernières heures d’informations en fonction des sélections correspondantes effectuées.

La vignette de compteur, sous la liste déroulante, cumule le nombre total de clusters Azure Data Explorer dans les abonnements sélectionnés et indique combien sont sélectionnés. Il existe des codes de couleur conditionnels pour les colonnes : Connexion persistante, UC, Utilisation de l’ingestion et Utilisation du cache. Les cellules codées en orange affichent des valeurs qui ne sont pas viables pour le cluster. 

Pour mieux comprendre ce que chaque métrique représente, nous vous recommandons de lire la documentation relative aux [métriques Azure Data Explorer](/azure/data-explorer/using-metrics#cluster-metrics).

### <a name="query-performance-tab"></a>Onglet Performances des requêtes

Cet onglet affiche la durée de la requête, le nombre total de requêtes simultanées, et le nombre total de requêtes limitées.

![Capture d’écran de l’onglet Performances des requêtes](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>Onglet Performances d’ingestion

Cet onglet affiche la latence d’ingestion, les résultats des ingestions réussies, les résultats des ingestions qui ont échoué, le volume d’ingestion, et les événements traités pour Event/IoT Hubs.

[![Capture d’écran de l’onglet Performances d’ingestion](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>Onglet Performances d’ingestion de streaming

Cet onglet fournit des informations sur le débit moyen de données, la durée moyenne et le taux de demandes.

### <a name="export-performance-tab"></a>Onglet Performances d’exportation

Cet onglet fournit des informations sur les enregistrements exportés, le retard, le nombre en attente, et le pourcentage d’utilisation pour les opérations d’exportation continues.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Vue d’une ressource de cluster Azure Data Explorer (analyse approfondie)

Pour accéder à des clusters Azure Monitor pour Azure Data Explorer directement à partir d’un cluster Azure Data Explorer :

1. Dans le portail Azure, sélectionnez **Clusters Azure Data Explorer**.

2. Dans la liste, choisissez un cluster Azure Data Explorer. Dans la section Supervision, choisissez **Insights (préversion)** .

Vous pouvez également accéder à ces vues en sélectionnant le nom de ressource d’un cluster Azure Data Explorer à partir de la vue Insights d’Azure Monitor.

Azure Monitor pour Azure Data Explorer combine les journaux d’activité et les métriques pour fournir une solution de supervision globale. L’inclusion de visualisations basées sur les journaux oblige les utilisateurs à [activer la journalisation des diagnostics de leur cluster Azure Data Explorer et à les envoyer à un espace de travail Log Analytics](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs). Les journaux de diagnostic qui doivent être activés sont les suivants : **Command**, **Query**, **TableDetails** et **TableUsageStatistics**.

![Capture d’écran du bouton bleu qui affiche le texte « Activer les journaux pour la supervision »](./media/data-explorer/enable-logs.png)


 L’onglet **Vue d’ensemble** affiche les éléments suivants :

- Des vignettes de métriques mettant en évidence la disponibilité et l’état global du cluster pour évaluer rapidement son intégrité.

- Un résumé des [recommandations Advisor](/azure/data-explorer/azure-advisor) actives et de l’état [d’intégrité des ressources](/azure/data-explorer/monitor-with-resource-health).

- Des graphiques présentant les principaux consommateurs d’UC et de mémoire, ainsi que le nombre d’utilisateurs uniques au fil du temps.


[![Capture d’écran de la vue d’une ressource de cluster Azure Data Explorer](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

L’onglet **Métriques clés** affiche une vue unifiée de certaines métriques du cluster, regroupées par métriques générales, métriques liées aux requêtes, métriques liées à l’ingestion, et métriques liées à l’ingestion de streaming.

[![Capture d’écran de la vue Échecs](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

L’onglet **Utilisation** permet aux utilisateurs d’approfondir les performances des commandes et des requêtes du cluster. Dans cette page, vous pouvez :
 
 - Identifier les utilisateurs et les applications qui envoient le plus de requêtes ou qui consomment le plus de ressources processeur et mémoire (afin d’identifier les utilisateurs qui soumettent les requêtes les plus lourdes que le cluster devra traiter).
 - Identifier les principaux utilisateurs et les principales applications par requêtes ayant échoué.
 - Identifier les modifications récentes du nombre de requêtes par rapport à la moyenne quotidienne historique (au cours des 16 derniers jours), par utilisateur et par application.
 - Identifier les tendances et les pics de requêtes, de mémoire et de consommation du processeur par type d’utilisateur, d’application et de commande.

[![Capture d’écran de l’affichage des opérations avec des graphiques en anneau de la principale application par nombre de commandes et de requêtes, top des principaux par nombre de commandes et requêtes, et principales commandes par types de commande](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![Capture d’écran de l’affichage des opérations avec des graphiques en courbes du nombre de requêtes par application, mémoire totale par application et processeur total par application](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

L’onglet **Tables** affiche les propriétés les plus récentes et l’historique des propriétés des tables du cluster. Vous pouvez voir quelles tables consomment le plus d’espace, suivre l’historique de croissance par taille de table, les données à chaud ainsi que le nombre de lignes dans le temps.

L’onglet **Cache** permet aux utilisateurs d’analyser leurs modèles de requêtes réelles et de les comparer à la stratégie de cache configurée (pour chaque table). Vous pouvez identifier les tables utilisées par le plus grand nombre de requêtes ainsi que les tables qui ne sont pas interrogées, et adapter ainsi la stratégie de cache en conséquence. Vous pouvez obtenir des recommandations de stratégie de cache particulières sur des tables spécifiques dans Azure Advisor (actuellement, les recommandations de cache sont disponibles uniquement à partir du [principal tableau de bord Azure Advisor](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)), en fonction de la recherche des requêtes réelles des 30 derniers jours et d’une stratégie de cache non optimisée pour au moins 95 % des requêtes. Des recommandations de réduction du cache dans Azure Advisor sont disponibles pour les clusters « délimités par les données », ce qui signifie que le cluster a une faible utilisation du processeur et une faible ingestion, mais qu’en raison d’une capacité de données élevée, le cluster ne peut pas faire l’objet d’un scale-in ou d’un scale-down.

[![Capture d’écran des détails du cache](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

## <a name="pin-to-azure-dashboard"></a>Épingler au tableau de bord Azure

Vous pouvez épingler une des sections métriques (dans la perspective de mise à l'échelle) à un tableau de bord Azure en sélectionnant l’icône de punaise en haut à droite de la section.

![Capture d’écran de l’icône de punaise sélectionnée](./media/data-explorer/pin.png)

## <a name="customize-azure-monitor-for-azure-data-explorer-cluster"></a>Personnaliser Azure Monitor pour cluster Azure Data Explorer

Cette section présente les scénarios courants de modification du classeur pour le personnaliser en fonction de vos besoins d’analyse de données :
* Régler l’étendue du classeur pour qu’il sélectionne toujours un abonnement ou des clusters Azure Data Explorer particuliers
* Modifier les métriques dans la grille
* Modifier les seuils ou le rendu/les codes de couleur

Pour commencer les personnalisations, vous pouvez activez le mode d’édition en sélectionnant le bouton **Personnaliser** dans la barre d’outils supérieure.

![Capture d’écran du bouton Personnaliser](./media/data-explorer/customize.png)

Les personnalisations sont enregistrées dans un classeur personnalisé pour éviter de remplacer la configuration par défaut dans notre classeur publié. Les classeurs sont enregistrés au sein d’un groupe de ressources, soit dans la section Mes rapports qui est privée, soit dans la section Rapports partagés accessible à tous les utilisateurs ayant accès au groupe de ressources. Une fois que vous avez enregistré le classeur personnalisé, vous devez accéder à la galerie de classeurs pour le lancer.

![Capture d’écran de la galerie de classeurs](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>Dépannage

Pour obtenir des conseils généraux sur la résolution des problèmes, reportez-vous à [l’article de résolution des problèmes](troubleshoot-workbooks.md) pour les insights basés sur les workbooks.

Cette section est destinée à vous aider à diagnostiquer et résoudre certains des problèmes communs que vous êtes susceptible de rencontrer lors de l’utilisation d’Azure Monitor pour cluster Azure Data Explorer (préversion). La liste ci-dessous permet d’identifier les informations pertinentes pour un problème spécifique.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Pourquoi ne puis-je pas voir tous mes abonnements dans le sélecteur d’abonnement ?

Nous affichons uniquement les abonnements qui contiennent des clusters Azure Data Explorer, choisis dans le filtre d’abonnement sélectionné, et qui sont sélectionnés dans la rubrique « Répertoire + abonnement » dans l’en-tête du portail Azure.

![Capture d’écran du filtre d’abonnement](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>Pourquoi ne vois-je aucune donnée pour mon cluster Azure Data Explorer dans les sections Utilisation, Tables ou Cache ?

Pour afficher vos données basées sur les journaux, vous devrez [activer les journaux de diagnostic](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) pour chacun des clusters Azure Data Explorer que vous souhaitez analyser. Vous pouvez effectuer cette opération dans les paramètres de diagnostic pour chaque cluster. Vous devrez envoyer vos données à un espace de travail Log Analytics. Les journaux de diagnostic qui doivent être activés sont les suivants : Command, Query, TableDetails et TableUsageStatistics.

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>J’ai déjà activé les journaux pour mon cluster Azure Data Explorer, pourquoi ne puis-je toujours pas afficher mes données sous Commandes et Requêtes ?

Actuellement, les journaux de diagnostic ne fonctionnent pas de manière rétroactive. Par conséquent, les données ne commenceront à apparaître que lorsque des actions seront effectuées dans Azure Data Explorer. Par conséquent, cette opération peut prendre un certain temps, allant de quelques heures à une journée, selon le degré d’activité de votre cluster Azure Data Explorer.


## <a name="next-steps"></a>Étapes suivantes

Découvrez les scénarios que les classeurs sont conçus pour prendre en charge, comment créer et personnaliser des rapports existants, et bien plus encore en consultant la rubrique [Créer des rapports interactifs avec les classeurs Azure Monitor](../visualize/workbooks-overview.md).