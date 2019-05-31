---
title: Collecter et analyser les journaux d’activité Azure dans l’espace de travail Analytique de journal | Microsoft Docs
description: Collecter le journal d’activité Azure dans les journaux d’Azure Monitor et utiliser la solution de surveillance pour analyser et rechercher le journal d’activité Azure parmi tous vos abonnements Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.openlocfilehash: 5839fd40a128097e400f13acbe4fb6ef90c656b7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248128"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Collecter et analyser les journaux d’activité Azure dans l’espace de travail Analytique de journal dans Azure Monitor
Le [journal d’activité Azure](activity-logs-overview.md) fournit des informations sur les événements de niveau d’abonnement qui se sont produites dans votre abonnement Azure. Cet article décrit comment collecter le journal d’activité dans un espace de travail Analytique de journal et comment utiliser l’Analytique de journal d’activité [solution de surveillance](../insights/solutions.md), qui fournit des requêtes de journal et de vues pour analyser ces données. 

Connexion du journal d’activité à un espace de travail Analytique de journal offre les avantages suivants :

- Consolider le journal d’activité à partir de plusieurs abonnements Azure dans un emplacement pour l’analyse.
- Store les entrées de journal d’activité pour plus de 90 jours.
- Mettre en corrélation des données de journal d’activité avec d’autres données d’analyse collectées par Azure Monitor.
- Utilisez [enregistrer des requêtes](../log-query/log-query-overview.md) pour effectuer une analyse complexe et obtenir des informations détaillées sur les entrées de journal d’activité.

## <a name="connect-to-log-analytics-workspace"></a>Se connecter à l’espace de travail Analytique de journal
Un journal d’activité peuvent être connecté à un seul espace de travail, mais un seul espace de travail peut être connecté au journal d’activité pour plusieurs abonnements dans le même locataire Azure. Pour la collection sur plusieurs locataires, consultez [locataires de collecter des journaux d’activité Azure dans un espace de travail Analytique de journal entre des abonnements dans différents Azure Active Directory](activity-log-collect-tenants.md).

Utilisez la procédure suivante pour connecter le journal d’activité à votre espace de travail Analytique de journal :

1. À partir de la **espaces de travail Analytique de journal** menu dans le portail Azure, sélectionnez l’espace de travail pour collecter le journal d’activité.
1. Dans le **Sources de données d’espace de travail** section du menu de l’espace de travail, sélectionnez **journal des activités Azure**.
1. Cliquez sur l’abonnement que vous souhaitez vous connecter.

    ![Workspaces](media/activity-log-export/workspaces.png)

1. Cliquez sur **Connect** pour connecter le journal d’activité dans l’abonnement à l’espace de travail sélectionné. Si l’abonnement est déjà connecté à un autre espace de travail, cliquez sur **déconnexion** pour tout d’abord le déconnecter.

    ![Connecter des espaces de travail](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Analyser dans l’espace de travail Analytique de journal
Lorsque vous vous connectez à un journal d’activité à un espace de travail Analytique de journal, les entrées sont écrites à l’espace de travail dans une table appelée **AzureActivity** que vous pouvez extraire avec une [requête de journal](../log-query/log-query-overview.md). La structure de cette table varie selon le [catégorie de l’entrée de journal](activity-logs-overview.md#categories-in-the-activity-log). Consultez [schéma d’événement de journal d’activité Azure](activity-log-schema.md) pour obtenir une description de chaque catégorie.

## <a name="activity-logs-analytics-monitoring-solution"></a>Solution de surveillance activité Analytique des journaux
La solution de surveillance d’Analytique de journal Azure inclut plusieurs requêtes de journal et les vues pour analyser les enregistrements de journal d’activité dans votre espace de travail Analytique de journal.

### <a name="install-the-solution"></a>Installer la solution
Utilisez la procédure décrite dans [installer une solution de surveillance](../insights/solutions.md#install-a-monitoring-solution) pour installer le **Analytique de journal d’activité** solution. Il n’existe aucune configuration supplémentaire requise.

### <a name="use-the-solution"></a>Utiliser la solution
Solutions de surveillance sont accessibles à partir de la **moniteur** menu dans le portail Azure. Sélectionnez **plus** dans le **Insights** section pour ouvrir la **vue d’ensemble** page avec les vignettes des solutions. Le **journaux d’activité Azure** vignette affiche le nombre de **AzureActivity** enregistrements dans votre espace de travail.

![Vignette Journaux d’activité Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Cliquez sur le **journaux d’activité Azure** vignette pour ouvrir le **journaux d’activité Azure** vue. L’affichage comprend les parties de visualisation dans le tableau suivant. Chaque partie répertorie jusqu'à 10 éléments correspondant aux critères de ce parties pour l’intervalle de temps spécifié. Vous pouvez exécuter une requête de journal qui retourne tous les enregistrements correspondants en cliquant sur **afficher tout** en bas de la partie.

![Tableau de bord Journaux d’activité Azure](media/collect-activity-logs/activity-log-dash.png)

| Composant de visualisation | Description |
| --- | --- |
| Entrées de journal d’activité Azure | Affiche un graphique à barres de l’entrée du journal d’activité Azure haut totaux des enregistrements de la plage de dates que vous avez sélectionné et affiche la liste des appelants de 10 activité supérieure. Cliquez sur le graphique à barres pour exécuter une recherche dans les journaux de `AzureActivity`. Cliquez sur un élément appelant pour exécuter une recherche de journal renvoie toutes les entrées de journal d’activité pour cet élément. |
| Journaux d’activité par état | Affiche un graphique en anneau pour l’état du journal d’activité Azure pour la plage de dates sélectionnée et la liste des premiers enregistrements d’état de dix. Cliquez sur le graphique pour exécuter une requête de journal pour `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`. Cliquez sur un élément d’état pour exécuter une recherche de journal qui renvoie toutes les entrées de journal d’activité pour cet enregistrement d’état. |
| Journaux d’activité par ressource | Indique le nombre total de ressources avec les journaux d’activité et répertorie les principales dix ressources avec l’enregistrement de compte pour chaque ressource. Cliquez sur la zone Total pour exécuter une recherche dans les journaux de `AzureActivity | summarize AggregatedValue = count() by Resource`, qui affiche toutes les ressources Azure disponibles pour la solution. Cliquez sur une ressource pour exécuter une requête de journal renvoyant tous les enregistrements d’activité pour cette ressource. |
| Journaux d’activité par fournisseur de ressources | Affiche le nombre total de fournisseurs de ressources qui génèrent des journaux d’activité et répertorie les dix premiers. Cliquez sur la zone total pour exécuter une requête de journal pour `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`, qui affiche tous les fournisseurs de ressources Azure. Cliquez sur un fournisseur de ressources pour exécuter une requête de journal retournant tous les enregistrements d’activité pour le fournisseur. |

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [journal d’activité](activity-logs-overview.md).
- En savoir plus sur la [plateforme de données Azure Monitor](data-platform.md).
- Utilisez [enregistrer des requêtes](../log-query/log-query-overview.md) pour afficher des informations détaillées à partir de votre journal d’activité.
