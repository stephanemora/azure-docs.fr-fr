---
title: Superviser les applications logiques avec Azure Monitor
description: Obtenez des insights et des données de débogage vous permettant de diagnostiquer et de résoudre les problèmes des exécutions de votre application logique avec les journaux Azure Monitor.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/29/2019
ms.openlocfilehash: 305b50c86a468354f049fcc57fcb79b537e8dfed
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791902"
---
# <a name="get-insights-and-debugging-data-for-logic-apps-by-using-azure-monitor-logs"></a>Obtenez des insights et des données de débogage pour des applications logiques à l’aide des journaux Azure Monitor

Pour superviser vos applications logiques et obtenir des informations de débogage plus détaillées à leur sujet, activez les [journaux Azure Monitor](../log-analytics/log-analytics-overview.md) quand vous créez les applications logiques. Les journaux Azure Monitor assurent la journalisation des diagnostics et la supervision des applications logiques quand la solution Logic Apps Management est installée dans le portail Azure. Cette solution fournit également des informations agrégées sur les exécutions de l’application logique avec certains détails spécifiques comme l’état, la durée d’exécution, l’état de la nouvelle soumission et les ID de corrélation. Cet article montre comment activer les journaux Azure Monitor pour voir les données et événements d’exécution associés aux exécutions de l’application logique.

Cette rubrique montre comment configurer des journaux Azure Monitor lorsque vous créez votre application logique. Si vous souhaitez activer les journaux Azure Monitor sur une application logique existante, effectuez ces étapes pour [activer la journalisation des diagnostics et envoyer aux journaux Azure Monitor les données d’exécution de l’application logique](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

> [!NOTE]
> Cette page expliquait auparavant comment effectuer ces tâches avec Microsoft Operations Management Suite (OMS), qui a été [mis hors service en janvier 2019](../azure-monitor/platform/oms-portal-transition.md), et ces étapes sont remplacées par les [journaux Azure Monitor](../azure-monitor/platform/data-platform-logs.md) qui ont succédé à Log Analytics. Les données de journal sont toujours stockées dans un espace de travail Log Analytics, et elles sont toujours collectées et analysées par le même service Log Analytics. Pour plus d'informations, consultez [Changements de terminologie Azure Monitor](../azure-monitor/terminology.md).

## <a name="prerequisites"></a>Prérequis

Avant de commencer, il vous faut un espace de travail Log Analytics. Découvrez [comment créer un espace de travail Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

## <a name="turn-on-logging-for-new-logic-apps"></a>Activer la journalisation pour les nouvelles applications logiques

1. Dans le [portail Azure](https://portal.azure.com), créez votre application logique. Dans le menu principal Azure, choisissez **Créer une ressource** > **Intégration** > **Application logique**.

   ![Créer une application logique](media/logic-apps-monitor-your-logic-apps-oms/create-new-logic-app.png)

1. Sous **Application logique**, procédez comme suit :

   1. Nommez votre application logique, puis sélectionnez votre abonnement Azure.

   1. Créez ou sélectionnez un groupe de ressources Azure. Sélectionnez l’emplacement de votre application logique.

   1. Sous **Log Analytics**, sélectionnez **Activer**.

   1. Dans la liste **d’espaces de travail Log Analytics**, sélectionnez l’espace de travail vers lequel vous souhaitez envoyer les données concernant les exécutions de votre application logique.

      ![Spécifier les informations de l’application logique](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app-details.png)

      Une fois cette étape terminée, Azure crée l’application logique qui est désormais associée à votre espace de travail Log Analytics. De plus, cette étape installe automatiquement la solution Logic Apps Management dans votre espace de travail Log Analytics.

   1. Sélectionnez **Créer** lorsque vous avez terminé.

1. Pour voir les exécutions de votre application logique, [effectuez ces étapes](#view-logic-app-runs-oms).

## <a name="install-logic-apps-management-solution"></a>Installer la solution Logic Apps Management

Si vous avez déjà configuré les journaux Azure Monitor lors de la création de votre application logique, ignorez cette étape. En effet, vous disposez déjà de la solution Logic Apps Management.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Tous les services**. Dans la zone de recherche, entrez « Espaces de travail Log Analytics », puis sélectionnez **Espaces de travail Log Analytics**.

   ![Sélectionnez « Espaces de travail Log Analytics »](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. Sous **Espaces de travail Log Analytics**, sélectionnez votre espace de travail.

   ![Sélectionnez votre espace de travail Log Analytics.](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. Dans le volet Vue d’ensemble, sous **Bien démarrer avec Log Analytics** > **Configurer des solutions de monitoring**, sélectionnez **Afficher les solutions**.

   ![Sélectionnez « Afficher les solutions »](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. Sous **Vue d’ensemble**, sélectionnez **Ajouter**.

   ![Sélectionnez « Ajouter ».](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

1. Une fois la **Place de marché** ouverte, dans la zone de recherche, entrez « logic apps management », puis sélectionnez **Logic Apps Management**.

   ![Sélectionner « Logic Apps Management »](./media/logic-apps-monitor-your-logic-apps-oms/select-logic-apps-management.png)

1. Dans le volet de description de la solution, sélectionnez **Créer**.

   ![Sélectionnez « Créer » pour « Logic Apps Management »](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

1. Vérifiez et confirmez l’espace de travail Log Analytics dans lequel vous souhaitez installer la solution, puis sélectionnez à nouveau **Créer**.

   ![Sélectionnez « Créer » pour « Logic Apps Management »](./media/logic-apps-monitor-your-logic-apps-oms/confirm-log-analytics-workspace.png)

   Une fois qu’Azure a déployé la solution sur le groupe de ressources Azure qui contient votre espace de travail Log Analytics, la solution apparaît dans le volet récapitulatif de votre espace de travail.

   ![Volet récapitulatif de l'espace de travail](./media/logic-apps-monitor-your-logic-apps-oms/workspace-summary-pane-logic-apps-management.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>Afficher des informations d’exécution sur l’application logique

Après l’exécution de votre application logique, vous pouvez afficher l’état et le nombre de ces exécutions sur la vignette **Logic Apps Management**.

1. Accédez à votre espace de travail Log Analytics, puis sélectionnez **Résumé de l’espace de travail** > **Logic Apps Management**.

   ![État et nombre d’exécutions de l’application logique](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary.png)

   Les exécutions de votre application logique y sont regroupées par nom ou par état d’exécution. Cette page affiche également des détails sur les échecs dans les actions ou déclencheurs pour les exécutions d’une application logique.

   ![Récapitulatif des états pour les exécutions de votre application logique](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary-details.png)

1. Pour afficher toutes les exécutions d’une application logique ou un état, sélectionnez la ligne correspondant à cette application logique ou à cet état.

   Voici un exemple qui montre toutes les exécutions d’une application logique :

   ![Voir les exécutions et l’état des applications logiques](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Cette page a des options avancées : 

   * Colonne **Propriétés suivies** : Pour une application logique dans laquelle vous configurez des propriétés suivies, qui sont regroupées par actions, vous pouvez voir ces propriétés à partir de cette colonne. Pour voir ces propriétés suivies, sélectionnez **Afficher**. Pour rechercher les propriétés suivies, utilisez le filtre de colonne.

      ![Afficher les propriétés suivies pour une application de logique](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

      Quand vous ajoutez de nouvelles propriétés suivies, 10 à 15 minutes peuvent s’écouler avant leur apparition. Découvrez [comment ajouter des propriétés suivies à votre application logique](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Soumettre à nouveau** : Vous pouvez resoumettre une ou plusieurs exécutions d’application logique ayant échoué ou réussi, ou en cours d’exécution. Cochez les cases correspondant aux exécutions que vous voulez resoumettre, puis sélectionnez **Soumettre à nouveau**.

     ![Soumettre à nouveau des exécutions d’application logique](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Pour filtrer vos résultats, vous pouvez effectuer un filtrage côté client et côté serveur.

   * **Filtre côté client** : Pour chaque colonne, sélectionnez les filtres que vous souhaitez, par exemple :

     ![Exemples de filtres de colonne](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Filtre côté serveur** : Pour sélectionner une fenêtre de temps spécifique ou pour limiter le nombre d’exécutions affichées, utilisez la commande d’étendue située en haut de la page. Par défaut, vous ne pouvez afficher que 1 000 enregistrements à la fois.

     ![Modifier la fenêtre de temps](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)

1. Pour voir toutes les actions et leurs détails pour une exécution spécifique, sélectionnez la ligne correspondant à une exécution d’application logique.

   Voici un exemple qui montre toutes les actions et tous les déclencheurs pour une exécution d’application logique spécifique :

   ![Afficher les actions d’une exécution d’application logique](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)

1. Pour voir la requête derrière les résultats ou tous les résultats, dans n’importe quelle page de résultats, sélectionnez **Afficher tout**, ce qui ouvre la page **Journaux**.

   ![Voir tous les résultats](media/logic-apps-monitor-your-logic-apps-oms/logic-app-see-all.png)

   Dans la page **Journaux**, vous pouvez choisir ces options :

   * Pour afficher les résultats de la requête dans une table, sélectionnez **Table**.

   * Les requêtes utilisent le [langage de requête Kusto](https://aka.ms/LogAnalyticsLanguageReference), que vous pouvez modifier si vous voulez voir des résultats différents. Pour changer la requête, mettez à jour la chaîne de requête, puis sélectionnez **Exécuter** pour voir les résultats dans le tableau. 

     ![Log Analytics - Vue de la requête](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Étapes suivantes

* [Surveiller les messages B2B](../logic-apps/logic-apps-monitor-b2b-message.md)