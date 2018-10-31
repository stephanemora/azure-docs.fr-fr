---
title: Superviser les exécutions des applications logiques avec Log Analytics - Azure Logic Apps | Microsoft Docs
description: Obtenir des insights et des données de débogage sur les exécutions de votre application logique avec Log Analytics à des fins de diagnostic et de résolution de problèmes
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/11/2018
ms.openlocfilehash: 177c361734a88acab5fc10d6b460645be82bf437
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457135"
---
# <a name="monitor-and-get-insights-about-logic-app-runs-with-log-analytics"></a>Surveiller et comprendre les exécutions de votre application logique à l’aide de Log Analytics

Pour la surveillance et l’obtention d’informations de débogage plus détaillées, vous pouvez activer Log Analytics lorsque vous créez une application logique. Log Analytics fournit des options de journalisation des diagnostics et de surveillance des exécutions de votre application logique dans le portail Azure. Lorsque vous ajoutez la solution Logic Apps Management, vous obtenez l’état agrégé des exécutions de votre application logique, ainsi que des informations détaillées telles que l’état, la durée d’exécution, l’état de la nouvelle soumission et les ID de corrélation.

Cet article montre comment activer Log Analytics afin de voir les données et les événements d’exécution associés aux exécutions de votre application logique.

 > [!TIP]
 > Pour surveiller vos applications logiques existantes, effectuez les étapes nécessaires pour [activer la journalisation des diagnostics et envoyer à Log Analytics les données d’exécution de l’application logique](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

## <a name="requirements"></a>Configuration requise

Avant de commencer, vous devez disposer d’un espace de travail Log Analytics. Découvrez [comment créer un espace de travail Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). 

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

## <a name="install-the-logic-apps-management-solution"></a>Installer la solution Logic Apps Management

Si vous avez déjà activé Log Analytics lors de la création de votre application logique, ignorez cette étape. En effet, vous disposez déjà de la solution Logic Apps Management.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Tous les services**. Dans la zone de recherche, entrez « log analytics » comme filtre, puis sélectionnez **Log Analytics**.

   ![Sélectionner « Log Analytics »](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. Sous **Log Analytics**, recherchez et sélectionnez votre espace de travail Log Analytics. 

   ![Sélectionnez votre espace de travail Log Analytics.](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. Sous **Configurer des solutions de supervision**, choisissez **Afficher les solutions**.

   ![Choisir « Afficher les solutions »](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. Sur la page Vue d’ensemble, choisissez **Ajouter** pour ouvrir la liste **Solutions de gestion**. Dans cette liste, sélectionnez **Logic Apps Management**. 

   ![Sélectionner « Logic Apps Management »](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

   Si vous ne trouvez pas la solution, choisissez **Charger plus** en bas de la liste jusqu’à ce que la solution s’affiche.

1. Choisissez **Créer**, ce qui permet d’installer la solution.

   ![Sélection de l’option « Ajouter » pour « Logic Apps Management »](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-runs-in-log-analytics-workspace"></a>Afficher les exécutions de l’application logique dans l’espace de travail Log Analytics

1. Pour voir le nombre et l’état des exécutions de votre application logique, accédez à votre espace de travail Log Analytics et ouvrez la page Vue d’ensemble. 

   Les détails des exécutions de votre application logique sont affichés dans la vignette **Logic Apps Management**. Pour afficher un récapitulatif plus détaillé des exécutions de votre application logique, choisissez la vignette **Logic Apps Management**. 

   ![Vignette de présentation affichant l’état et le nombre d’exécutions de l’application logique](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

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

   * **Soumettre à nouveau :** vous pouvez soumettre à nouveau une ou plusieurs exécutions d’application logique ayant échoué, réussies ou en cours d’exécution. Cochez les cases correspondant aux exécutions que vous souhaitez soumettre à nouveau, puis choisissez **Soumettre à nouveau**. 

     ![Soumettre à nouveau des exécutions d’application logique](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Pour filtrer ces résultats, vous pouvez effectuer un filtrage côté client et côté serveur.

   * **Filtre côté client** : pour chaque colonne, choisissez les filtres que vous souhaitez, par exemple :

     ![Exemples de filtres de colonne](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Filtre côté serveur** : pour choisir une fenêtre de temps ou pour limiter le nombre d’exécutions affichées, utilisez la commande d’étendue située en haut de la page. Par défaut, vous ne pouvez afficher que 1 000 enregistrements à la fois.
   
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
     
     Sur la page Azure Log Analytics, vous pouvez mettre à jour des requêtes et afficher les résultats dans un tableau. Cette requête utilise le [langage de requête Kusto](https://aka.ms/LogAnalyticsLanguageReference), que vous pouvez modifier si vous souhaitez afficher des résultats différents. 

     ![Azure Log Analytics - Vue de la requête](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Étapes suivantes

* [Surveiller les messages B2B](../logic-apps/logic-apps-monitor-b2b-message.md)