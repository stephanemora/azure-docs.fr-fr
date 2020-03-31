---
title: Se connecter à Trello à partir d’Azure Logic Apps
description: Automatiser les tâches et les flux de travail qui surveillent et gèrent les listes, les tableaux et les cartes dans vos projets Trello à l’aide d’Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5c4fcb9b4fea1a4d982b5cf665564599d371b7cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789135"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Surveiller et gérer Trello avec Azure Logic Apps

Avec Azure Logic Apps et le connecteur Trello, vous pouvez créer des tâches et des flux de travail automatisés qui surveillent et gèrent vos listes, cartes, tableaux et membres d’équipes Trello, etc., par exemple :

* Surveiller l’ajout de nouvelles cartes à des tableaux et à des listes. 
* Créer, obtenir et gérer des tableaux, des cartes et des listes.
* Ajouter des commentaires et des membres à des cartes.
* Répertorier des tableaux, des étiquettes de tableaux, des cartes de tableaux, des commentaires de cartes, des membres de cartes, des membres d’équipes et des équipes dont vous faites partie. 
* Obtenir des équipes.

Vous pouvez utiliser des déclencheurs qui obtiennent des réponses de votre compte Trello et mettent la sortie à la disposition d’autres actions. Vous pouvez utiliser des actions qui effectuent des tâches avec votre compte Trello. Vous pouvez également faire en sorte que des actions utilisent le résultat d’actions Trello. Par exemple, quand une nouvelle carte est ajoutée au tableau ou à la liste, vous pouvez envoyer des messages avec le connecteur Slack. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/). 

* Votre compte et vos informations d’identification utilisateur Trello

  Vos informations d’identification autorisent votre application logique à créer une connexion et à accéder à votre compte Trello.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique à partir de laquelle vous souhaitez accéder à votre compte Trello. Pour démarrer avec un déclencheur Trello, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser une action Trello, démarrez votre application logique avec un déclencheur, par exemple, le déclencheur **Périodicité**.

## <a name="connect-to-trello"></a>Se connecter à Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Pour les applications logiques vides, dans la zone de recherche, entrez « trello » comme filtre. Dans la liste des déclencheurs, sélectionnez le déclencheur souhaité. 

   -ou-

   Pour les applications logiques existantes, sous la dernière étape où vous souhaitez ajouter une action, choisissez **Nouvelle étape**. 
   Dans la zone de recherche, entrez « trello » comme filtre. 
   Dans la liste des actions, sélectionnez l’action souhaitée.

   Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. 
   Cliquez sur le signe plus ( **+** ) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Si vous êtes invité à vous connecter à Trello, faites-le maintenant afin de pouvoir autoriser l’accès pour votre application logique et vous connecter.

1. Fournissez les informations nécessaires pour le déclencheur ou l’action sélectionnés et continuez à générer le flux de travail de votre application logique.

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence](/connectors/trello/) du connecteur.

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)