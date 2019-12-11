---
title: Attendre des événements et y répondre
description: Automatiser les flux de travail qui déclenchent, suspendent et reprennent en fonction des événements au niveau d’un point de terminaison de service à l’aide d’Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 10/10/2019
tags: connectors
ms.openlocfilehash: 7ff411ae082acfe2d465ab9d3371982b0693c226
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74787044"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Créer et exécuter des flux de travail automatisés basés sur des événements à l’aide de Webhooks HTTP dans Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) et le connecteur Webhook HTTP intégré vous permettent d’automatiser des flux de travail qui attendent et s’exécutent en fonction d’événements spécifiques qui se produisent sur point de terminaison HTTP ou HTTPS en générant des applications logiques. Par exemple, vous pouvez créer une application logique qui surveille un point de terminaison de service dans l’attente d’un événement spécifique avant de déclencher le flux de travail et d’exécuter les actions spécifiées, plutôt que de vérifier ou d’*interroger* ce point de terminaison périodiquement.

Voici des exemples de flux de travail basés sur un événement :

* Attendre qu’un élément provienne d’un [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) avant de déclencher l’exécution d’une application logique.
* Attendre une approbation avant de poursuivre un flux de travail.

## <a name="how-do-webhooks-work"></a>Comment fonctionnent les webhooks ?

Un déclencheur de Webhook HTTP est basé sur un événement et ne dépend pas de la vérification ou de l’interrogation périodiques de nouveaux éléments. Lorsque vous enregistrez une application logique avec un déclencheur de Webhook, ou lorsque vous modifiez votre application logique en l’activant, le déclencheur de Webhook *s’abonne* à un service ou à un point de terminaison spécifiques en inscrivant une *URL de rappel* avec ce service ou point de terminaison. Le déclencheur attend ensuite que ce service ou point de terminaison appelle l’URL, ce qui a pour effet de démarrer l’exécution de l’application logique. Comme le [déclencheur de demande](connectors-native-reqres.md), l’application logique se déclenche dès que l’événement spécifié se produit. Le déclencheur *se désabonne* du service ou du point de terminaison si vous supprimez le déclencheur et enregistrez votre application logique, ou lorsque vous modifiez votre application logique en la désactivant.

Une action de Webhook HTTP est également basée sur un événement et *s’abonne* à un service ou à un point de terminaison spécifiques en inscrivant une *URL de rappel* avec ce service ou point de terminaison. L’action du Webhook suspend le flux de travail de l’application logique et attend que le service ou le point de terminaison appellent l’URL avant de reprendre l’exécution de l’application logique. L’application logique *se désabonne* du service ou du point de terminaison dans les cas suivants :

* Lorsque l’action de webhook s’est terminée avec succès.
* Si l’exécution de l’application logique est annulée en attendant une réponse.
* Avant que la logique d’application arrive à expiration.

Par exemple, l’action [**Envoyer un e-mail d’approbation**](connectors-create-api-office365-outlook.md) du connecteur Office 365 Outlook est un exemple d’action de Webhook qui suit ce modèle. Vous pouvez étendre ce modèle à tout service à l’aide de l’action de Webhook.

> [!NOTE]
> Logic Apps applique le protocole TLS (Transport Layer Security) 1.2 lors de la réception du rappel sur le déclencheur ou l’action Webhook HTTP. Si vous constatez des erreurs de liaison SSL, assurez-vous d’utiliser le protocole TLS 1.2.

Pour plus d’informations, consultez les rubriques suivantes :

* [Paramètres de déclencheur de Webhook HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhooks et abonnements](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Créer des API personnalisées qui prennent en charge un Webhook](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* URL d’un point de terminaison ou d’une API déjà déployés prenant en charge le modèle d’abonnement et de désabonnement de Webhook pour des [déclencheurs de Webhook](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) ou des [actions de Webhook](../logic-apps/logic-apps-create-api-app.md#webhook-actions) dans des applications logiques, selon le cas.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md). Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

* Application logique dans laquelle vous souhaitez attendre des événements spécifiques au point de terminaison cible. Pour commencer à utiliser le déclencheur de Webhook HTTP, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser l’action de Webhook HTTP, démarrez votre application logique avec un déclencheur de votre choix. Cet exemple utilise le déclencheur HTTP en tant que première étape.

## <a name="add-an-http-webhook-trigger"></a>Ajouter un déclencheur de Webhook HTTP

Ce déclencheur intégré inscrit une URL de rappel avec le service spécifié, et attend que celui-ci envoie une requête HTTP POST à cette URL. Lorsque cet événement se produit, le déclencheur s’active et exécute immédiatement l’application logique.

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Ouvrez votre application logique vide dans le Concepteur d’application logique.

1. Dans le concepteur, dans la zone de recherche, entrez « http webhook » en tant que filtre. Dans la liste **Déclencheurs**, sélectionnez le déclencheur **Webhook HTTP**.

   ![Sélectionner le déclencheur Webhook HTTP](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Cet exemple renomme le déclencheur « Déclencheur de Webhook HTTP » afin que l’étape porte un nom plus descriptif. Par ailleurs, l’exemple ajoute ensuite une action de Webhook HTTP, et les deux noms doivent être uniques.

1. Spécifiez les valeurs des [paramètres du déclencheur de Webhook HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) que vous souhaitez utiliser pour les appels d’abonnement et de désabonnement, par exemple :

   ![Entrer les paramètres du déclencheur de Webhook HTTP](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. Pour ajouter d’autres paramètres disponibles, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez les paramètres de votre choix.

   Pour en savoir plus sur les types d’authentification disponibles pour Webhook HTTP, consultez [Ajouter l’authentification aux appels sortants](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Continuez à générer le flux de travail de votre application logique avec des actions qui s’exécutent quand le déclencheur se déclenche.

1. Lorsque vous avez terminé, pensez à enregistrer votre application logique. Dans la barre d’outils du concepteur, sélectionnez **Enregistrer**.

   L’enregistrement de votre application logique a pour effet d’appeler le point de terminaison d’abonnement et d’inscrire l’URL de rappel pour le déclenchement de cette application logique.

1. Dorénavant, chaque fois que le service cible envoie une demande `HTTP POST` à l’URL de rappel, l’application logique se déclenche et inclut toutes les données transmises via la requête.

## <a name="add-an-http-webhook-action"></a>Ajouter une action de Webhook HTTP

Cette action intégrée inscrit une URL de rappel avec le service spécifié, suspend le flux de travail de l’application logique, et attend que ce service envoie une requête HTTP POST à cette URL. Lorsque cet événement se produit, l’action reprend l’exécution de l’application logique.

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Ouvrez votre application logique dans le Concepteur d’applications logiques.

   Cet exemple utilise le déclencheur de Webhook HTTP en tant que première étape.

1. Sous l’étape où vous souhaitez ajouter l’action de Webhook HTTP, sélectionnez **Étape suivante**.

   Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. Cliquez sur le signe ( **+** ) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Dans le concepteur, dans la zone de recherche, entrez « http webhook » en tant que filtre. Dans la liste **Actions**, sélectionnez l’action **Webhook HTTP**.

   ![Sélectionner l’action de Webhook HTTP](./media/connectors-native-webhook/select-http-webhook-action.png)

   Cet exemple renomme l’action « Action de Webhook HTTP » afin qu’elle ait un nom plus descriptif.

1. Spécifiez les valeurs des paramètres d’action de Webhook HTTP, qui sont similaires aux [paramètres du déclencheur de Webhook HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger) que vous souhaitez utiliser pour les appels d’abonnement et de désabonnement, par exemple :

   ![Entrer des paramètres d’action de Webhook HTTP](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   Pendant l’exécution du runtime, l’application logique appelle le point de terminaison d’abonnement lors de l’exécution de cette action. Votre application logique suspend ensuite le flux de travail et attend que le service cible envoie une demande `HTTP POST` à l’URL de rappel. Si l’action aboutit, elle se désabonne du point de terminaison, et votre application logique reprend l’exécution du flux de travail.

1. Pour ajouter d’autres paramètres disponibles, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez les paramètres de votre choix.

   Pour en savoir plus sur les types d’authentification disponibles pour Webhook HTTP, consultez [Ajouter l’authentification aux appels sortants](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Lorsque vous avez terminé, pensez à enregistrer votre application logique. Dans la barre d’outils du concepteur, sélectionnez **Enregistrer**.

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations sur les paramètres de déclencheur et d’action, qui sont similaires entre eux, voir [Paramètres de Webhook HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger).

### <a name="output-details"></a>Détails des résultats

Voici d’autres informations sur les sorties d’un déclencheur ou d’une action de Webhook HTTP qui renvoient les informations suivantes :

| Nom de la propriété | type | Description |
|---------------|------|-------------|
| headers | object | En-têtes de la requête |
| body | object | Objet JSON | Objet avec le contenu du corps de la requête |
| Code d’état | int | Code d’état de la requête |
|||

| Code d’état | Description |
|-------------|-------------|
| 200 | OK |
| 202 | Acceptée |
| 400 | Demande incorrecte |
| 401 | Non autorisé |
| 403 | Interdit |
| 404 | Introuvable |
| 500 | Erreur interne du serveur. Une erreur inconnue s’est produite. |
|||

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
