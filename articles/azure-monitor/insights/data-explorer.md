---
title: Azure Data Explorer Insights | Microsoft Docs
description: Cet article décrit comment utiliser Azure Data Explorer Insights.
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: b522e510968b97d80917bc6fa4123d33e4480cbf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662207"
---
# <a name="azure-data-explorer-insights"></a>Azure Data Explorer Insights

Azure Data Explorer Insights fournit une supervision complète de vos clusters grâce à une vue unifiée des performances, des opérations, de l’utilisation et des défaillances de vos clusters.

Il offre ce qui suit :

-    **Perspective à grande échelle**. Une vue instantanée des principales métriques de vos clusters vous aide à suivre les performances des requêtes, l’ingestion et les opérations d’exportation.
-   **Analyse approfondie**. Vous pouvez effectuer une analyse détaillée d’un cluster Azure Data Explorer particulier.
-    **Personnalisation**. Vous pouvez choisir les métriques que vous souhaitez voir, modifier ou définir des seuils qui s’alignent sur vos limites, et enregistrer vos propres classeurs personnalisés. Les graphiques d’un classeur peuvent être épinglés aux tableaux de bord Azure.

Cet article vous aidera à comprendre comment intégrer et utiliser Azure Data Explorer Insights.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Affichage à partir d’Azure Monitor (perspective à grande échelle)

À partir d’Azure Monitor, vous pouvez afficher les principales métriques de performances du cluster. Ces métriques incluent des informations sur les requêtes, l’ingestion et les opérations d’exportation de plusieurs clusters dans votre abonnement. Elles peuvent vous aider à identifier les problèmes de performances.

Pour afficher les performances de vos clusters sur l’ensemble de vos abonnements

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Sélectionnez **Surveillance** dans le volet gauche. Dans la section **Hub Insights**, sélectionnez **Clusters Azure Data Explorer**.

![Capture d’écran des sélections permettant d’afficher les performances des clusters Azure Data Explorer.](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>Onglet Overview

Dans l’onglet **Vue d’ensemble** de l’abonnement sélectionné, le tableau affiche les métriques interactives des clusters Azure Data Explorer regroupés dans l’abonnement. Vous pouvez filtrer les résultats en fonction des options que vous sélectionnez dans les listes déroulantes suivantes :

* **Abonnements** : seuls les abonnements qui comportent des clusters Azure Data Explorer sont listés.

* **Clusters Azure Data Explorer** : par défaut, jusqu’à cinq clusters sont présélectionnés. Si vous sélectionnez tous les clusters ou plusieurs d’entre eux dans le sélecteur d’étendue, jusqu’à 200 clusters seront retournés.

* **Intervalle de temps** : par défaut, le tableau affiche les 24 dernières heures d’informations en fonction des sélections correspondantes effectuées.

La vignette de compteur, sous la liste déroulante, indique le nombre total de clusters Azure Data Explorer dans les abonnements sélectionnés, et combien sont sélectionnés. Il existe des codages de couleur conditionnels pour les colonnes : **Keep Alive**, **Processeur**, **Utilisation de l’ingestion** et **Utilisation du cache**. Les cellules codées en orange affichent des valeurs qui ne sont pas viables pour le cluster. 

Pour mieux comprendre ce que chaque métrique représente, nous vous recommandons de lire la documentation relative aux [métriques Azure Data Explorer](/azure/data-explorer/using-metrics#cluster-metrics).

### <a name="query-performance-tab"></a>Onglet Performances des requêtes

L’onglet **Performances des requêtes** affiche la durée des requêtes, le nombre total de requêtes simultanées et le nombre total de requêtes limitées.

![Capture d’écran de l’onglet Performances des requêtes.](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>Onglet Performances d’ingestion

L’onglet **Performances de l’ingestion** affiche la latence d’ingestion, les résultats des ingestions réussies, les résultats des ingestions qui ont échoué, le volume d’ingestion, et les événements traités pour les hubs d’événements et les hubs IoT.

[![Capture d’écran de l’onglet Performances de l’ingestion.](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>Onglet Performances d’ingestion de streaming

L’onglet **Performances de l’ingestion en streaming** fournit des informations sur le débit moyen de données, la durée moyenne et le taux de demandes.

### <a name="export-performance-tab"></a>Onglet Performances d’exportation

L’onglet **Performances d’exportation** fournit des informations sur les enregistrements exportés, le retard, le nombre en attente, et le pourcentage d’utilisation pour les opérations d’exportation continues.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Vue d’une ressource de cluster Azure Data Explorer (analyse approfondie)

Pour accéder à Azure Data Explorer Insights directement à partir d’un cluster Azure Data Explorer

1. Dans le portail Azure, sélectionnez **Clusters Azure Data Explorer**.

2. Dans la liste, choisissez un cluster Azure Data Explorer. Dans la section de supervision, sélectionnez **Insights**.

Vous pouvez également accéder à ces vues en sélectionnant le nom de ressource d’un cluster Azure Data Explorer à partir de la vue Insights d’Azure Monitor.

> [!NOTE]
> Azure Data Explorer Insights combine les journaux et les métriques pour fournir une solution de supervision globale. L’inclusion de visualisations basées sur les journaux oblige les utilisateurs à [activer la journalisation des diagnostics de leur cluster Azure Data Explorer et à les envoyer à un espace de travail Log Analytics](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs). Les journaux de diagnostic qui doivent être activés sont **Command**, **Query**, **SucceededIngestion**, **FailedIngestion**, **IngestionBatching**, **TableDetails** et **TableUsageStatistics**. (L’activation des journaux **SucceededIngestion** peut être coûteuse. Activez-les uniquement si vous avez besoin de superviser les ingestions ayant réussi.)

![Capture d’écran du bouton permettant de configurer les journaux pour la supervision.](./media/data-explorer/enable-logs.png)

### <a name="overview-tab"></a>Onglet Overview

L’onglet **Vue d’ensemble** affiche les éléments suivants :

- Les vignettes de métriques qui mettent en évidence la disponibilité et l’état global du cluster pour une évaluation rapide de l’intégrité

- Un résumé des [recommandations Azure Advisor](/azure/data-explorer/azure-advisor) actives et de l’état d’[intégrité des ressources](/azure/data-explorer/monitor-with-resource-health)

- Des graphiques présentant les principaux consommateurs de processeur et de mémoire, ainsi que le nombre d’utilisateurs uniques au fil du temps

[![Capture d’écran de la vue d’une ressource de cluster Azure Data Explorer.](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

### <a name="key-metrics-tab"></a>Onglet Métriques clés

L’onglet **Métriques clés** affiche une vue unifiée de certaines métriques du cluster. Elles sont regroupées en métriques générales, métriques liées à la requête, métriques liées à l’ingestion et métriques liées à l’ingestion en streaming.

[![Capture d’écran des graphiques sous l’onglet Métriques clés.](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

### <a name="usage-tab"></a>Onglet Utilisation

L’onglet **Utilisation** permet aux utilisateurs d’approfondir les performances des commandes et des requêtes du cluster. Dans cet onglet, vous pouvez :
 
- Identifier les groupes de charge de travail, les utilisateurs et les applications qui envoient le plus de requêtes ou qui consomment le plus de ressources processeur et mémoire. Vous pouvez alors identifier les charges de travail qui envoient les requêtes les plus lourdes à traiter par le cluster.
- Identifier les principaux groupes de charge de travail, utilisateurs et applications par requêtes ayant échoué.
- Identifier les modifications récentes du nombre de requêtes, par rapport à la moyenne quotidienne historique (au cours des 16 derniers jours), par groupe de charges de travail, par utilisateur et par application.
- Identifier les tendances et les pics de nombre de requêtes, de mémoire et de consommation du processeur par groupe de charges de travail, utilisateur, application et type de commande.

[![Capture d’écran de la vue des opérations avec des graphiques en anneau relatifs aux commandes et aux requêtes.](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![Capture d’écran de la vue des opérations avec des graphiques en courbe relatifs aux requêtes et à la mémoire.](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

### <a name="tables-tab"></a>Onglet Tables

L’onglet **Tables** affiche les propriétés les plus récentes et l’historique des propriétés des tables du cluster. Cela vous permet d’identifier les tables qui consomment le plus d’espace. Vous pouvez également suivre l’historique de croissance par taille de table, les données à chaud ainsi que le nombre de lignes dans le temps.

### <a name="cache-tab"></a>Onglet Cache

L’onglet **Cache** permet aux utilisateurs d’analyser leurs modèles de fenêtre de recherche en arrière des requêtes réelles, et de les comparer à la stratégie de cache configurée (pour chaque table). Vous pouvez identifier les tables utilisées par le plus grand nombre de requêtes ainsi que les tables qui ne sont pas interrogées, et adapter ainsi la stratégie de cache en conséquence. 

Vous pouvez recevoir des recommandations de stratégie de cache sur des tables spécifiques dans Azure Advisor. Actuellement, les recommandations de cache sont uniquement disponibles à partir du [tableau de bord principal Azure Advisor](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations). Elles sont basées sur la fenêtre de recherche en arrière des requêtes réelles au cours des 30 derniers jours et sur une stratégie de cache non optimisée pour au moins 95 % des requêtes. 

Des recommandations de réduction du cache dans Azure Advisor sont disponibles pour les clusters « délimités par les données ». Cela signifie que le cluster a une faible utilisation du processeur et une faible ingestion, mais qu’en raison d’une capacité de données élevée, le cluster ne peut pas faire l’objet d’un scale-in ou d’un scale-down.

[![Capture d’écran des détails du cache.](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

### <a name="cluster-boundaries-tab"></a>Onglet Limites du cluster

L’onglet **Limites du cluster** montre les limites du cluster en fonction de votre utilisation. Sous cet onglet, vous pouvez inspecter l’utilisation du cache, le processeur et l’ingestion. Ces métriques sont notées **Basse**, **Moyenne** ou **Élevée**. Ces métriques et scores sont importants lors du choix du nombre d’instances et de la référence SKU optimale pour votre cluster. Elles sont prises en compte dans la recommandation de SKU/taille d’Azure Advisor. 

Sous cet onglet, vous pouvez sélectionner une vignette de métrique et effectuer une exploration approfondie afin de comprendre sa tendance et comment son score est déterminé. Vous pouvez également afficher la recommandation de SKU/taille d’Azure Advisor pour votre cluster. Par exemple, dans l’image suivante, vous pouvez voir que toutes les métriques sont notées **Basse**. Le cluster reçoit une recommandation de coût qui lui permettra de subir un scale-in ou scale-out et de réduire les coûts.

> [!div class="mx-imgBorder"]
> [![Capture d’écran des limites de cluster.](./media/data-explorer/cluster-boundaries.png)](./media/data-explorer/cluster-boundaries.png#lightbox)

## <a name="pin-to-an-azure-dashboard"></a>Épingler à un tableau de bord Azure

Vous pouvez épingler l’une des sections de métriques (dans la perspective « à grande échelle ») à un tableau de bord Azure en sélectionnant l’icône de punaise en haut à droite de la section.

![Capture d’écran de l’icône de punaise sélectionnée.](./media/data-explorer/pin.png)

## <a name="customize-azure-data-explorer-insights"></a>Personnaliser Azure Data Explorer Insights

Vous pouvez modifier le classeur pour le personnaliser afin de prendre en charge vos besoins d’analytique des données :
* Régler l’étendue du classeur pour qu’il sélectionne toujours un abonnement ou des clusters Azure Data Explorer particuliers
* Changer les métriques dans la grille
* Changer les seuils ou le rendu/les codes de couleur

Vous pouvez commencer les personnalisations en sélectionnant le bouton **Personnaliser** dans la barre d’outils supérieure.

![Capture d’écran du bouton Personnaliser.](./media/data-explorer/customize.png)

Les personnalisations sont enregistrées dans un classeur personnalisé, afin d’éviter de remplacer la configuration par défaut dans un classeur publié. Les classeurs sont enregistrés au sein d’un groupe de ressources, soit dans la section **Mes rapports** qui est privée ou dans la section **Rapports partagés** accessible à tous les utilisateurs ayant accès au groupe de ressources. Une fois que vous avez enregistré le classeur personnalisé, accédez à la galerie de classeurs pour l’ouvrir.

![Capture d’écran de la galerie de classeurs.](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>Résolution des problèmes

Pour obtenir des conseils généraux sur la résolution des problèmes, consultez l’article de [résolution des problèmes liés aux insights basés sur des classeurs](troubleshoot-workbooks.md).

Les sections suivantes vous aideront à diagnostiquer et à résoudre certains problèmes courants que vous pouvez rencontrer lors de l’utilisation d’Azure Data Explorer Insights.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Pourquoi ne puis-je pas voir tous mes abonnements dans le sélecteur d’abonnement ?

Azure Data Explorer Insights affiche uniquement les abonnements qui contiennent les clusters Azure Data Explorer choisis à partir du filtre d’abonnement sélectionné. Vous sélectionnez un filtre d’abonnement sous **Répertoire + abonnement** dans le portail Azure.

![Capture d’écran de la sélection d’un filtre d’abonnement.](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-dont-i-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-section"></a>Pourquoi ne vois-je aucune donnée pour mon cluster Azure Data Explorer dans la section Utilisation, Tables ou Cache ?

Pour afficher vos données basées sur les journaux, vous devrez [activer les journaux de diagnostic](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) pour chaque cluster Azure Data Explorer que vous souhaitez superviser. Vous pouvez effectuer cette opération dans les paramètres de diagnostic pour chaque cluster. Vous devrez envoyer vos données à un espace de travail Log Analytics. Les journaux de diagnostic qui doivent être activés sont **Command**, **Query**, **TableDetails** et **TableUsageStatistics**.

### <a name="ive-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>J’ai déjà activé les journaux pour mon cluster Azure Data Explorer. Pourquoi ne puis-je toujours pas voir mes données sous Commandes et Requêtes ?

Actuellement, les journaux de diagnostic ne fonctionnent pas rétroactivement. Les données commencent à apparaître une fois que des actions ont été effectuées dans Azure Data Explorer. Cela peut prendre un certain temps, allant de quelques heures à une journée, selon le degré d’activité de votre cluster Azure Data Explorer.

## <a name="next-steps"></a>Étapes suivantes

Découvrez les scénarios que les classeurs sont conçus pour prendre en charge, comment créer et personnaliser des rapports existants, et bien plus encore en consultant la rubrique [Créer des rapports interactifs avec les classeurs Azure Monitor](../visualize/workbooks-overview.md).
