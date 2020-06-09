---
title: Se connecter à Project Online à partir d’Azure Logic Apps
description: Automatisez les flux de travail qui surveillent, créent et gèrent des projets, des tâches et des ressources Project Online à l’aide d’Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: a3e90fa3e3f57c1575a7ab09f9ce6941c13adcd1
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834864"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Gérer des projets, des tâches et des ressources Project Online à l’aide d’Azure Logic Apps

Avec Azure Logic Apps et le connecteur Project Online, vous pouvez créer des tâches et des flux de travail automatisés pour vos projets, tâches et ressources dans Project Online via Office 365. Vos flux de travail peuvent effectuer ces actions et d’autres, par exemple :

* Surveiller la création de projets, de tâches ou de ressources. Ou surveiller la publication de nouveaux projets.
* Créer des projets, des tâches ou des ressources nouveaux.
* Faire une liste des tâches ou des projets existants.
* Extraire, archiver ou publier des projets.

Grâce à des capacités de gestion de projets performantes, Project Online vous aide à planifier, à hiérarchiser et à gérer des projets et des investissements de portefeuilles de projets depuis presque n’importe où et sur pratiquement tous les appareils. Vous pouvez utiliser des déclencheurs Project Online qui obtiennent des réponses de Project Online et mettent le résultat à la disposition d’autres actions. Vous pouvez utiliser des actions dans vos applications logiques pour effectuer des tâches variées dans Project Online. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/). 

* Project Online, disponible via un [compte Office 365](https://www.office.com/), 

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique à partir de laquelle vous souhaitez accéder à vos données Project Online. Pour démarrer avec un déclencheur Project Online, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser une action Project Online, démarrez votre application logique avec un autre déclencheur, par exemple, le déclencheur **Périodicité**.

## <a name="connect-to-project-online"></a>Se connecter à Project Online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Choisissez une procédure : 

   * Pour les applications logiques vides, dans la zone de recherche, entrez « Project Online » comme filtre. 
   Dans la liste des déclencheurs, sélectionnez le déclencheur souhaité. 

     -ou-

   * Pour les applications logiques existantes, sous l’étape où vous souhaitez ajouter une action, choisissez **Nouvelle étape**. Pour les applications logiques vides, dans la zone de recherche, entrez « Project Online » comme filtre. Dans la liste des actions, sélectionnez l’action souhaitée.

1. Si vous êtes invité à vous connecter à Project Online, faites-le maintenant.

   Vos informations d’identification autorisent votre application logique à créer une connexion à Project Online et à accéder à votre données.

1. Fournissez les informations nécessaires pour le déclencheur ou l’action sélectionnés et continuez à générer le flux de travail de votre application logique.

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence](/connectors/projectonline/) du connecteur.

## <a name="get-support"></a>Obtenir de l’aide

* Pour toute question, consultez la [page de questions Microsoft Q&A pour Azure Logic Apps](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)