---
title: Se connecter à des flux RSS à partir d’Azure Logic Apps
description: Automatiser des tâches et des flux de travail qui surveillent et gèrent les flux RSS à l’aide d’Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 1e95b7bfce3d57dfe579a5622c3c13fac2b6af68
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834813"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Gérer des flux RSS à l’aide d’Azure Logic Apps

Avec Azure Logic Apps et le connecteur RSS, vous pouvez créer des tâches et des flux de travail automatisés pour tous les flux RSS, par exemple :

* Surveiller si les éléments de flux RSS sont publiés.
* Afficher la liste de tous les éléments du flux RSS.

RSS (Rich Site Summary), également appelé Really Simple Syndication, est un format courant pour la syndication web, utilisé pour la publication de contenu fréquemment mis à jour, tel que les billets de blogs et les titres d’actualité. De nombreux éditeurs de contenu fournissent un flux RSS auquel les utilisateurs peuvent s’abonner. 

Vous pouvez utiliser un déclencheur RSS qui obtient des réponses d’un flux RSS et met la sortie à la disposition d’autres actions. Vous pouvez utiliser une action RSS dans vos applications logiques pour effectuer une tâche avec le flux RSS. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/). 

* L’URL d’un flux RSS

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique à partir de laquelle vous souhaitez accéder à un flux RSS. Pour démarrer avec un déclencheur RSS, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser une action RSS, démarrez votre application logique avec un autre déclencheur, par exemple, le déclencheur **Périodicité**.

## <a name="connect-to-an-rss-feed"></a>Se connecter à un flux RSS

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Choisissez une procédure : 

   * Pour les applications logiques vides, dans la zone de recherche, entrez « rss » comme filtre. Dans la liste des déclencheurs, sélectionnez le déclencheur souhaité. 

     -ou-

   * Pour les applications logiques existantes, sous l’étape où vous souhaitez ajouter une action, choisissez **Nouvelle étape**. Dans la zone de recherche, entrez « rss » comme filtre. Dans la liste des actions, sélectionnez l’action souhaitée.

1. Fournissez les informations nécessaires pour le déclencheur ou l’action sélectionnés et continuez à générer le flux de travail de votre application logique.

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence](/connectors/rss/) du connecteur.

## <a name="get-support"></a>Obtenir de l’aide

* Pour toute question, consultez la [page de questions Microsoft Q&A pour Azure Logic Apps](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)