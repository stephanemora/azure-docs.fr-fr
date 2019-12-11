---
title: Se connecter à Wunderlist à partir d’Azure Logic Apps
description: Automatiser les tâches et les flux de travail qui supervisent et gèrent les listes, les tâches, les rappels et bien plus encore dans votre compte Wunderlist à l’aide d’Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5ac13595bd77238aaede5fa3bdc3a35ef69e8504
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789118"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Superviser et gérer Wunderlist à l’aide d’Azure Logic Apps

Avec Azure Logic Apps et le connecteur Wunderlist, vous pouvez créer des tâches et des flux de travail automatisés qui supervisent et gèrent les tâches, les rappels et les listes de tâches et bien plus encore dans votre compte Wunderlist, ainsi que d’autres actions, par exemple :

* Superviser quand des tâches sont créées ou doivent être exécutées ou quand des rappels se produisent.
* Créer et gérer des listes, notes, tâches, tâches subordonnées et bien plus encore.
* Définir des rappels.
* Obtenir des listes, tâches, tâches subordonnées, rappels, fichiers, notes, commentaires et bien plus encore.

[Wunderlist](https://www.wunderlist.com/) est un service qui vous permet de planifier, gérer et terminer vos projets, listes de tâches et tâches sur l’appareil de votre choix, où que vous soyez. Vous pouvez utiliser des déclencheurs qui obtiennent des réponses de votre compte Wunderlist et mettent la sortie à la disposition d’autres actions. Vous pouvez utiliser des actions qui effectuent des tâches avec votre compte Wunderlist. Vous pouvez également faire en sorte que des actions utilisent la sortie d’actions Wunderlist. Par exemple, quand de nouvelles tâches doivent être exécutées, vous pouvez publier des messages avec le connecteur Slack. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/). 

* Vos informations d’identification utilisateur et votre compte Wunderlist

   Vos informations d’identification autorisent votre application logique à créer une connexion et à accéder à votre compte Wunderlist.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique à partir de laquelle vous souhaitez accéder à votre compte Yammer. Pour démarrer avec un déclencheur Wunderlist, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser une action Wunderlist, démarrez votre application logique avec un autre déclencheur, par exemple, le déclencheur **Périodicité**.

## <a name="connect-to-wunderlist"></a>Se connecter à Wunderlist

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Choisissez une procédure : 

   * Pour les applications logiques vides, dans la zone de recherche, entrez « wunderlist » comme filtre. 
   Dans la liste des déclencheurs, sélectionnez le déclencheur souhaité. 

     -ou-

   * Pour les applications logiques existantes : 
   
     * Sous la dernière étape où vous souhaitez ajouter une action, choisissez **Nouvelle étape**. 

       -ou-

     * Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. 
     Cliquez sur le signe plus ( **+** ) qui s’affiche, puis sélectionnez **Ajouter une action**.
     
       Dans la zone de recherche, entrez « wunderlist » comme filtre. 
       Sous la liste des actions, sélectionnez l’action souhaitée.

1. Si vous êtes invité à vous connecter à Wunderlist, faites-le maintenant afin de pouvoir autoriser l’accès.

1. Fournissez les informations nécessaires pour le déclencheur ou l’action sélectionnés et continuez à générer le flux de travail de votre application logique.

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence](/connectors/wunderlist/) du connecteur.

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)