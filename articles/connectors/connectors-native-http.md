---
title: Se connecter à un point de terminaison HTTP avec Azure Logic Apps | Microsoft Docs
description: Automatiser les tâches et les flux de travail qui communiquent avec n’importe quel point de terminaison HTTP à l’aide d’Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 95a039c990dc9b6c852a69518f0a71c51a4747b7
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302620"
---
# <a name="call-http-or-https-endpoints-with-azure-logic-apps"></a>Appeler des points de terminaison HTTP ou HTTPS avec Azure Logic Apps

Avec Azure Logic Apps et le connecteur HTTP (Hypertext Transfer Protocol), vous pouvez automatiser les flux de travail qui communiquent avec n’importe quel point de terminaison HTTP ou HTTPS en générant des applications logiques. Par exemple, vous pouvez superviser le point de terminaison de service pour votre site web. Quand un événement se produit à ce point de terminaison, tel qu’une panne de votre site web, l’événement déclenche le flux de travail de votre application logique et exécute les actions spécifiées. 

Vous pouvez utiliser le déclencheur HTTP comme première étape de votre flux de travail pour vérifier ou *interroger* un point de terminaison selon une planification régulière. À chaque vérification, le déclencheur envoie un appel ou une *demande* au point de terminaison. La réponse du point de terminaison détermine si le flux de travail de votre application logique s’exécute. Le déclencheur transmet le contenu de la réponse aux actions de votre application logique. 

Vous pouvez utiliser l’action HTTP comme toute autre étape de votre flux de travail pour appeler le point de terminaison quand vous le souhaitez. La réponse du point de terminaison détermine la façon dont s’exécutent les actions restantes de votre flux de travail.

Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. 

* L’URL du point de terminaison cible que vous souhaitez appeler 

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique à partir de laquelle vous voulez appeler le point de terminaison cible. Pour démarrer avec le déclencheur HTTP, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser l’action HTTP, démarrez votre application logique avec un déclencheur.

## <a name="add-http-trigger"></a>Ajouter un déclencheur HTTP

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique vide dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Dans la zone de recherche, entrez « http » en tant que filtre. Sous la liste des déclencheurs, sélectionnez le déclencheur **HTTP**. 

   ![Sélectionner le déclencheur HTTP](./media/connectors-native-http/select-http-trigger.png)

1. Indiquez les [paramètres et valeurs du déclencheur HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) à inclure dans l’appel au point de terminaison cible. Configurez la fréquence à laquelle le déclencheur doit vérifier le point de terminaison cible.

   ![Entrer les paramètres du déclencheur HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Pour plus d’informations sur le déclencheur HTTP, les paramètres et les valeurs, consultez [Informations de référence sur les types d’actions et de déclencheurs](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger).

1. Continuez à générer le flux de travail de votre application logique avec des actions qui s’exécutent quand le déclencheur se déclenche.

## <a name="add-http-action"></a>Ajouter une action HTTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Sous la dernière étape où vous souhaitez ajouter l’action HTTP, choisissez **Nouvelle étape**. 

   Dans cet exemple, l’application logique démarre avec le déclencheur HTTP en tant que première étape.

1. Dans la zone de recherche, entrez « http » en tant que filtre. Dans la liste des actions, sélectionnez l’action **HTTP**.

   ![Sélection de l’action HTTP](./media/connectors-native-http/select-http-action.png)

   Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. 
   Cliquez sur le signe plus (**+**) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Indiquez les [paramètres et valeurs de l’action HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) à inclure dans l’appel au point de terminaison cible. 

   ![Entrer les paramètres de l’action HTTP](./media/connectors-native-http/http-action-parameters.png)

1. Quand vous avez terminé, veillez à enregistrer votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**. 

## <a name="authentication"></a>Authentification

Pour définir l’authentification, choisissez **Afficher les options avancées** à l’intérieur de l’action ou du déclencheur. Pour plus d’informations sur les types d’authentification disponibles pour les déclencheurs et les actions HTTP, consultez [Informations de référence sur les types d’actions et de déclencheurs](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
