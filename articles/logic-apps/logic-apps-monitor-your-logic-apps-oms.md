---
title: Surveiller des applications logiques avec les journaux d’Azure Monitor - Azure Logic Apps | Microsoft Docs
description: Obtenez des insights et des données de débogage vous permettant de diagnostiquer et de résoudre les problèmes des exécutions de votre application logique avec Azure Log Analytics.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 3f890e6cabd757fdd38374befaaccd1a10c9bd96
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106208"
---
# <a name="monitor-logic-apps-with-azure-monitor-logs"></a>Superviser les applications logiques avec les journaux d’activité Azure Monitor

Pour surveiller et obtenir des informations de débogage plus détaillées sur vos applications logiques, activez [Azure Monitor enregistre](../log-analytics/log-analytics-overview.md) lorsque vous créez votre application logique. Journaux d’Azure Monitor fournit des diagnostics de journalisation et de surveillance pour vos applications logiques lorsque vous installez la solution Logic Apps Management dans le portail Azure. Cette solution fournit également des informations agrégées sur les exécutions de l’application logique avec certains détails spécifiques comme l’état, la durée d’exécution, l’état de la nouvelle soumission et les ID de corrélation. Cet article explique comment activer les journaux Azure Monitor afin que vous pouvez afficher les événements du runtime et les données de votre application logique s’exécute.

Pour activer les journaux Azure Monitor pour les applications logiques existantes, suivez ces étapes pour [activer la journalisation des diagnostics et envoyer les données de runtime d’application logique dans les journaux d’Azure Monitor](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

> [!NOTE]
> Cette page expliquait auparavant comment effectuer ces tâches avec Microsoft Operations Management Suite (OMS) ; dans la mesure où celui-ci sera [mis hors service en janvier 2019](../azure-monitor/platform/oms-portal-transition.md), ces étapes sont remplacées par Azure Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer, il vous faut un espace de travail Log Analytics. Découvrez [comment créer un espace de travail Log Analytics](../azure-monitor/learn/quick-create-workspace.md). 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>Activer la journalisation des diagnostics lors de la création d’applications logiques

1. Dans le [portail Azure](https://portal.azure.com), créez une application logique. Choisissez **Créer une ressource** > **Intégration**  > **Application logique**.

   ![Créer une application logique](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

1. Sous **Créer une application logique**, effectuez les étapes suivantes :

   1. Nommez votre application logique, puis sélectionnez votre abonnement Azure. 

   1. Créez ou sélectionnez un groupe de ressources Azure.

   1. Définissez **Log Analytics** sur **Activé**. 

   1. Dans la liste d’espaces de travail Log Analytics, sélectionnez l’espace de travail vers lequel vous souhaitez envoyer des données concernant les exécutions de votre application logique. 

      ![Créer une application logique](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      Une fois cette étape terminée, Azure crée l’application logique qui est désormais associée à votre espace de travail Log Analytics. 
      De plus, cette étape installe automatiquement la solution Logic Apps Management dans votre espace de travail Log Analytics.

   1. Lorsque vous êtes prêt, choisissez **Créer**.

1. Pour voir les exécutions de votre application logique, [effectuez ces étapes](#view-logic-app-runs-oms).

## <a name="install-logic-apps-management-solution"></a>Installer la solution Logic Apps Management

Si vous avez déjà activé Azure Monitor connecté lorsque vous avez créé votre application logique, ignorez cette étape. En effet, vous disposez déjà de la solution Logic Apps Management.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Tous les services**. Dans la zone de recherche, entrez « Log Analytics », puis sélectionnez **Log Analytics**.

   ![Sélectionner « Log Analytics »](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. Sous **Log Analytics**, recherchez et sélectionnez votre espace de travail Log Analytics. 

   ![Sélectionnez votre espace de travail Log Analytics.](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. Sous **Bien démarrer avec Log Analytics** > **Configurer des solutions de monitoring**, choisissez **Afficher les solutions**.

   ![Choisir « Afficher les solutions »](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. Sur la page Vue d’ensemble, choisissez **Ajouter** pour ouvrir la liste **Solutions de gestion**. Dans cette liste, sélectionnez **Logic Apps Management**. 

   ![Sélectionner « Logic Apps Management »](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

   Si vous ne trouvez pas la solution, choisissez **Charger plus** en bas de la liste jusqu’à ce que la solution s’affiche.

1. Choisissez **Créer**, vérifiez l’espace de travail Log Analytics dans lequel vous souhaitez installer la solution, puis sélectionnez à nouveau **Créer**.   

   ![Choisir « Créer » pour « Logic Apps Management »](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

   Si vous ne souhaitez pas utiliser un espace de travail existant, vous pouvez également en créer un maintenant.

   Lorsque vous avez terminé, la solution Logic Apps Management s’affiche sur la page de présentation. 

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>Afficher des informations d’exécution sur l’application logique

Après l’exécution de votre application logique, vous pouvez afficher l’état et le nombre de ces exécutions sur la vignette **Logic Apps Management**. 

1. Accédez à votre espace de travail Log Analytics, puis ouvrez la page de présentation. Choisissez **Logic Apps Management**. 

   ![État et nombre d’exécutions de l’application logique](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

   Les exécutions de votre application logique y sont regroupées par nom ou par état d’exécution. 
   Cette page affiche également des détails sur les échecs dans les actions ou déclencheurs pour les exécutions d’une application logique.

   ![Récapitulatif des états pour les exécutions de votre application logique](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
1. Pour afficher toutes les exécutions d’une application logique ou un état, sélectionnez la ligne correspondant à cette application logique ou à cet état.

   Voici un exemple qui montre toutes les exécutions d’une application logique :

   ![Affichage des exécutions d’une application logique ou d’un état](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Cette page présente ces options avancées :

   * **Propriétés suivies :**

     Cette colonne affiche les propriétés suivies, regroupées par actions, pour l’application logique. Pour afficher les propriétés suivies, choisissez **Afficher**. 
     Pour rechercher les propriétés suivies, utilisez le filtre de colonne.
   
     ![Afficher les propriétés suivies pour une application de logique](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     Quand vous ajoutez de nouvelles propriétés suivies, 10 à 15 minutes peuvent s’écouler avant leur apparition. Découvrez [comment ajouter des propriétés suivies à votre application logique](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Soumettre à nouveau :** vous pouvez soumettre à nouveau une ou plusieurs exécutions d’application logique ayant échoué ou réussi, ou en cours d’exécution. Cochez les cases correspondant aux exécutions que vous souhaitez soumettre à nouveau, puis choisissez **Soumettre à nouveau**. 

     ![Soumettre à nouveau des exécutions d’application logique](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Pour filtrer ces résultats, vous pouvez effectuer un filtrage côté client et côté serveur.

   * **Filtre côté client** : pour chaque colonne, choisissez les filtres que vous souhaitez, par exemple :

     ![Exemples de filtres de colonne](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Filtre côté serveur** : pour choisir une fenêtre de temps spécifique ou pour limiter le nombre d’exécutions affichées, utilisez la commande d’étendue située en haut de la page. Par défaut, vous ne pouvez afficher que 1 000 enregistrements à la fois.
   
     ![Modifier la fenêtre de temps](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
1. Pour afficher toutes les actions et leurs détails pour une exécution spécifique, sélectionnez une ligne correspondant à une exécution d’application logique.

   Voici un exemple qui montre toutes les actions pour une exécution d’application logique spécifique :

   ![Afficher les actions d’une exécution d’application logique](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)
   
1. Pour afficher la requête derrière les résultats ou afficher tous les résultats dans n’importe quelle page de résultats, choisissez **Tout afficher**. La page Recherche dans les journaux s’ouvre.
   
   ![Tout afficher dans les pages de résultats](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)
   
   Dans la page Recherche dans les journaux,

   * Pour afficher les résultats de la requête dans une table, choisissez **Table**.

   * Pour modifier la requête, modifiez la chaîne de requête dans la barre de recherche. 
   Pour une meilleure expérience, choisissez **Analytique avancée**.

     ![Affichage des actions et des détails relatifs à une exécution d’application logique](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)
     
     Dans la page d’analytique de journal, vous pouvez mettre à jour des requêtes et afficher les résultats de la table. Cette requête utilise le [langage de requête Kusto](https://aka.ms/LogAnalyticsLanguageReference), que vous pouvez modifier si vous souhaitez afficher des résultats différents. 

     ![analytique de journal - affichage des requêtes](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Étapes suivantes

* [Surveiller les messages B2B](../logic-apps/logic-apps-monitor-b2b-message.md)