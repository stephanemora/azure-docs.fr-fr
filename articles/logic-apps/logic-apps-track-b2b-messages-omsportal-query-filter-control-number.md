---
title: Créer des requêtes de suivi des messages B2B
description: Créer des requêtes qui effectuent le suivi des messages AS2, X12 et EDIFACT dans Log Analytics pour Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 36cf45aa0f7d46b62caa586d1939ec52e67b1a3e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792864"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-monitor-logs-for-azure-logic-apps"></a>Créer des requêtes de suivi des messages B2B dans les journaux Azure Monitor pour Azure Logic Apps

Pour rechercher des messages AS2, X12 ou EDIFACT que vous suivez avec les [journaux Azure Monitor](../log-analytics/log-analytics-overview.md), vous pouvez créer des requêtes qui filtrent les actions en fonction de critères spécifiques. Par exemple, vous pouvez rechercher des messages sur la base d’un numéro de contrôle d’échange spécifique.

> [!NOTE]
> Cette page expliquait auparavant comment effectuer ces tâches avec Microsoft Operations Management Suite (OMS) ; dans la mesure où celui-ci sera [mis hors service en janvier 2019](../azure-monitor/platform/oms-portal-transition.md), ces étapes sont remplacées par Azure Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prérequis

* Une application logique configurée avec une journalisation des diagnostics. Découvrez comment [créer une application logique](quickstart-create-first-logic-app-workflow.md) et comment [configurer la journalisation pour cette application logique](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Un compte d’intégration configuré avec une surveillance et une journalisation. Découvrez comment [créer un compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) et comment [configurer une surveillance et une journalisation pour ce compte](../logic-apps/logic-apps-monitor-b2b-message.md).

* Si ce n’est déjà fait, [publiez les données de diagnostic dans les journaux Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md), puis [configurez le suivi des messages dans les journaux Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="create-queries-with-filters"></a>Créer des requêtes avec des filtres

Pour rechercher des messages en fonction de propriétés ou de valeurs spécifiques, vous pouvez créer des requêtes qui utilisent des filtres. 

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Tous les services**. Dans la zone de recherche, entrez « Log Analytics », puis sélectionnez **Log Analytics**.

   ![Sélectionner Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. Sous **Log Analytics**, recherchez et sélectionnez votre espace de travail Log Analytics. 

   ![Sélectionner un espace de travail Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. Dans le menu de votre espace de travail, sous **Général**, sélectionnez **Journaux d’activité (vue classique)** ou **Journaux d’activité**. 

   Cet exemple montre comment utiliser la vue Journaux d’activité classique. 
   Si vous sélectionnez **Afficher les journaux d’activité** dans la section **Optimiser votre expérience Log Analytics**, sous **Rechercher et analyser des journaux d’activité**, vous obtenez la vue **Journaux d’activité (vue classique)** . 

   ![Vue Journaux d’activité classique](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. Dans la zone d’édition de la requête, commencez à taper le nom du champ que vous souhaitez trouver. Lorsque vous commencez à taper, l’éditeur de requête affiche les correspondances possibles et les opérations que vous pouvez utiliser. Une fois la requête créée, sélectionnez **Exécuter** ou appuyez sur la touche Entrée.

   Cet exemple effectue une recherche des correspondances sur **LogicAppB2B**. 
   Apprenez-en davantage sur la manière de [rechercher des données dans les journaux Azure Monitor](../log-analytics/log-analytics-log-searches.md).

   ![Commencer à taper la chaîne de requête](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. Pour modifier le délai d’exécution que vous souhaitez afficher, dans le volet de gauche, sélectionnez une durée dans la liste de durée ou faites glisser le curseur. 

   ![Modifier le délai d’exécution](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. Pour ajouter un filtre à votre requête, sélectionnez **Ajouter**. 

   ![Ajouter un filtre à une requête](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. Sous **Ajouter des filtres**, entrez le nom du filtre recherché. Si vous trouvez le filtre, sélectionnez-le. Dans le volet de gauche, sélectionnez **Ajouter** à nouveau.

   Par exemple, voici une autre requête qui effectue une recherche des événements **Type=="AzureDiagnostics"** et trouve des résultats en fonction du numéro de contrôle de l’échange en sélectionnant le filtre **event_record_messageProperties_interchangeControlNumber_s**.

   ![Sélectionner une valeur de filtre](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   Après avoir sélectionné **Ajouter**, votre requête est mise à jour avec l’événement et la valeur de filtre sélectionnés. 
   Vos résultats précédents sont à présent également filtrés. 

   Par exemple, cette requête recherche **Type=="AzureDiagnostics"** et trouve des résultats en fonction d’un numéro de contrôle de l’échange en utilisant le filtre **event_record_messageProperties_interchangeControlNumber_s**.

   ![Résultats filtrés](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>Enregistrer la requête

Pour enregistrer votre requête dans la vue **Journaux d’activité (vue classique)** , procédez comme suit :

1. Depuis votre requête sur la page **Journaux d’activité (vue classique)** , sélectionnez **Analytics**. 

   ![Sélectionnez « Analytics »](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. Dans la barre d’outils de la requête, cliquez sur **Enregistrer**.

   ![Choisir « Enregistrer »](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. Spécifiez les informations de votre requête. Par exemple, nommez votre requête, sélectionnez **Requête**et spécifiez un nom de catégorie. Une fois ces opérations effectuées, sélectionnez **Enregistrer**.

   ![Choisir « Enregistrer »](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. Pour afficher les requêtes enregistrées, revenez à la page de requête. Dans la barre d’outils de la requête, cliquez sur **Recherches enregistrées**.

   ![Sélectionnez « Recherches enregistrées »](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. Sous **Recherches enregistrées**, sélectionnez votre requête afin de pouvoir afficher les résultats. 

   ![Sélectionner votre requête](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   Pour mettre à jour la requête afin d’obtenir des résultats différents, modifiez la requête.

## <a name="find-and-run-saved-queries"></a>Rechercher et exécuter des requêtes enregistrées

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Tous les services**. Dans la zone de recherche, entrez « Log Analytics », puis sélectionnez **Log Analytics**.

   ![Sélectionner Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. Sous **Log Analytics**, recherchez et sélectionnez votre espace de travail Log Analytics. 

   ![Sélectionner un espace de travail Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. Dans le menu de votre espace de travail, sous **Général**, sélectionnez **Journaux d’activité (vue classique)** ou **Journaux d’activité**. 

   Cet exemple montre comment utiliser la vue Journaux d’activité classique. 

1. Une fois que la page de requête s’ouvre, dans la barre d’outils de la requête, sélectionnez **Recherches enregistrées**.

   ![Sélectionnez « Recherches enregistrées »](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. Sous **Recherches enregistrées**, sélectionnez votre requête afin de pouvoir afficher les résultats. 

   ![Sélectionner votre requête](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   La requête s’exécute automatiquement, mais si la requête ne s’exécute pas pour une raison quelconque, sélectionnez **Exécuter** dans l’éditeur de requête.

## <a name="next-steps"></a>Étapes suivantes

* [Schémas de suivi AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schémas de suivi X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schémas de suivi personnalisé](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)