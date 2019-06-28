---
title: Envoyer et recevoir des messages avec Azure Service Bus – Azure Logic Apps | Microsoft Docs
description: Configurer une messagerie cloud d’entreprise avec Azure Service Bus dans Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 68378c87e18df874059579445352b8fd1b2b6c13
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105578"
---
# <a name="exchange-messages-in-the-cloud-with-azure-service-bus-and-azure-logic-apps"></a>Échanger des messages dans le cloud avec Azure Service Bus et Azure Logic Apps

Avec Azure Logic Apps et le connecteur Azure Service Bus, vous pouvez créer des tâches et des workflows automatisés pour transférer des données (des ordres de vente, des bons de commande, des journaux ou des mouvements de stock par exemple) entre les applications pour votre organisation. Non seulement le connecteur surveille, envoie et gère les messages, mais il effectue également des actions relatives aux files d’attente, sessions, rubriques, abonnements, etc. Par exemple :

* Surveillance de la réception des messages (semi-automatique ou passage furtif) dans les files d’attente, les rubriques et les abonnements aux rubriques 
* Envoi de messages
* Création et suppression des abonnements de rubrique
* Gestion des messages dans les files d’attente et les abonnements aux rubriques, par exemple : obtention, obtention différée, exécution, report, abandon et lettre morte
* Renouvellement des verrous sur les messages et les sessions dans les files d’attente et les abonnements aux rubriques
* Clôture des sessions dans les rubriques et les files d’attente

Vous pouvez utiliser des déclencheurs afin d’obtenir des réponses de Service Bus et mettre la sortie à la disposition d’autres actions dans vos applications logiques. Vous pouvez également faire en sorte que d’autres actions utilisent la sortie d’actions Service Bus. Si vous ne connaissez pas Service Bus et Logic Apps, consultez [Qu’est-ce qu’Azure Service Bus ?](../service-bus-messaging/service-bus-messaging-overview.md) et [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. 

* Un espace de noms et une entité de messagerie Service Bus, telle une file d’attente. Si vous ne disposez pas de ces éléments, découvrez comment [créer votre espace de noms Service Bus et une file d’attente](../service-bus-messaging/service-bus-create-namespace-portal.md). 

  Ces éléments doivent exister dans le même abonnement Azure que vos applications logiques qui les utilisent.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique dans laquelle vous souhaitez utiliser Service Bus. Votre application logique doit exister dans le même abonnement Azure que votre bus des services. Pour démarrer avec Service Bus, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser une action Service Bus, démarrez votre application logique avec un autre déclencheur, par exemple, le déclencheur **Périodicité**.

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Vérifier les autorisations

Vérifiez que votre application logique dispose des autorisations pour accéder à l’espace de noms Service Bus. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 

2. Accédez à votre *espace de noms* Service Bus. Dans la page de l’espace de noms, sous **Paramètres**, sélectionnez **Stratégies d’accès partagé**. Sous **Revendications**, vérifiez que vous disposez des autorisations **Gérer** pour cet espace de noms.

   ![Gérer les autorisations pour votre espace de noms Service Bus](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Récupérez la chaîne de connexion pour votre espace de noms Service Bus. Vous en aurez besoin lorsque vous saisirez vos informations de connexion dans votre application logique.

   1. Sélectionnez **RootManageSharedAccessKey**. 
   
   1. En regard de votre chaîne de connexion principale, cliquez sur le bouton de copie. Enregistrez la chaîne de connexion en vue d’une utilisation ultérieure.

      ![Copier la chaîne de connexion de l’espace de noms Service Bus](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Pour vous assurer que votre chaîne de connexion est bien associée à votre espace de noms Service Bus ou à une entité de messagerie (une file d’attente par exemple), recherchez la chaîne de connexion pour le paramètre `EntityPath` . Si vous trouvez ce paramètre, la chaîne de connexion correspond à une entité spécifique. De fait, il ne s’agit pas de la chaîne appropriée à utiliser avec votre application logique.

## <a name="add-trigger-or-action"></a>Ajouter un déclencheur ou une action

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Pour ajouter un *déclencheur* à une application logique vide, dans la zone de recherche, entrez « Azure Service Bus » comme filtre. Dans la liste des déclencheurs, sélectionnez le déclencheur souhaité. 

   Par exemple, pour déclencher votre application logique quand un nouvel élément est envoyé à une file d’attente Service Bus, sélectionnez le déclencheur suivant : **Quand une file d’attente reçoit un message (saisie semi-automatique)**

   ![Sélectionner un déclencheur Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > Certains déclencheurs peuvent renvoyer un ou plusieurs messages, comme le déclencheur **Lorsqu’un ou plusieurs messages arrivent dans une file d’attente (saisie semi-automatique)** . Lorsque ces déclencheurs sont activés, ils retournent entre un et le nombre de messages spécifiés par la propriété **Nombre maximal de messages** du déclencheur.

   Tous les déclencheurs Service Bus sont des déclencheurs *d’interrogation longue*, ce qui signifie que lorsqu’un déclencheur est activé, celui-ci traite tous les messages puis attend 30 secondes le temps qu’un plus grand nombre de messages s’affichent dans la file d’attente ou la rubrique d’abonnement. 
   Si aucun message n’apparaît au bout de 30 secondes, l’exécution du déclencheur est ignorée. 
   Dans le cas contraire, le déclencheur poursuit la lecture des messages jusqu’à ce que la rubrique d’abonnement ou la file d’attente soit vide. La prochaine interrogation de déclencheur est basée sur l’intervalle de récurrence spécifié dans les propriétés du déclencheur.

1. Pour ajouter une *action* à une application logique existante, procédez comme suit : 

   1. Sous la dernière étape où vous souhaitez ajouter une action, choisissez **Nouvelle étape**. 

      Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. 
      Cliquez sur le signe plus ( **+** ) qui s’affiche, puis sélectionnez **Ajouter une action**.

   1. Dans la zone de recherche, entrez « Azure Service Bus » en tant que filtre. 
   Dans la liste des actions, sélectionnez l’action souhaitée. 
 
      Par exemple, sélectionnez cette action : **Envoyer un message**

      ![Sélectionner une action Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Si vous connectez votre application logique à votre espace de noms Service Bus pour la première fois, le concepteur d’application logique vous invite à fournir vos informations de connexion. 

   1. Fournissez un nom pour votre connexion, puis sélectionnez votre espace de noms Service Bus.

      ![Créer une connexion Service Bus, partie 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      Pour entrer manuellement la chaîne de connexion, choisissez **Entrer manuellement les informations de connexion**. 
      Si vous ne disposez pas de chaîne de connexion, découvrez [comment obtenir votre chaîne de connexion](#permissions-connection-string).

   1. Sélectionnez à présent votre stratégie Service Bus, puis choisissez **Créer**.

      ![Créer une connexion Service Bus, partie 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

1. Pour cet exemple, sélectionnez l’entité de messagerie souhaitée (par exemple, une file d’attente ou une rubrique). Dans cet exemple, sélectionnez votre file d’attente Service Bus. 
   
   ![Sélectionner la file d’attente Service Bus.](./media/connectors-create-api-azure-service-bus/service-bus-select-queue.png)

1. Fournissez les informations nécessaires pour le déclencheur ou l’action. Pour cet exemple, suivez les étapes correspondantes pour votre déclencheur ou action : 

   * **Pour l’exemple de déclencheur** : définissez l’intervalle d’interrogation et la fréquence de vérification de la file d’attente.

     ![Définir l’intervalle d'interrogation](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

     Lorsque vous avez terminé, continuez la création du workflow de votre application logique en ajoutant les actions que vous souhaitez. Par exemple, vous pouvez ajouter une action qui envoie un e-mail lorsqu’un nouveau message arrive.
     Lorsque votre déclencheur vérifie la file d’attente et trouve un nouveau message, l’application logique exécute les actions que vous avez sélectionnées pour le message trouvé.

   * **Pour l’exemple d’action** : entrez le contenu du message et tous les autres détails. 

     ![Fournir les détails et le contenu du message](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

     Lorsque vous avez terminé, continuez la création du workflow de votre application logique en ajoutant les actions que vous souhaitez. Par exemple, vous pouvez ajouter une action qui envoie un e-mail confirmant que votre message a été envoyé.

1. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence](/connectors/servicebus/) du connecteur.

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)