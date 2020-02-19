---
title: Appeler des points de terminaison HTTP et HTTPS
description: Envoyer des requêtes sortantes à des points de terminaison HTTP et HTTPS avec Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: 9c1b2af8d06c9466ed6c82308de941b43510238a
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117970"
---
# <a name="send-outgoing-calls-to-http-or-https-endpoints-by-using-azure-logic-apps"></a>Envoyer des appels sortants à des points de terminaison HTTP ou HTTPS avec Azure Logic Apps

Avec [Azure Logic Apps](../logic-apps/logic-apps-overview.md) et le déclencheur ou l’action HTTP intégrés, vous pouvez créer des tâches et des workflows automatisés qui envoient régulièrement des requêtes à un point de terminaison HTTP ou HTTPS quelconque. Pour recevoir et répondre aux appels HTTP ou HTTPS entrants, utilisez le [déclencheur de requête ou l’action Réponse](../connectors/connectors-native-reqres.md) intégrés.

Par exemple, vous pouvez surveiller le point de terminaison de service pour votre site web en vérifiant ce point de terminaison selon une planification définie. Lorsqu’un événement se produit au niveau de ce point de terminaison, tel qu’une panne de votre site web, l’événement déclenche le flux de travail de votre application logique et exécute les actions spécifiées.

Vous pouvez utiliser le déclencheur HTTP comme première étape de votre flux de travail pour vérifier ou *interroger* un point de terminaison selon une planification régulière. À chaque vérification, le déclencheur envoie un appel ou une *demande* au point de terminaison. La réponse du point de terminaison détermine si le flux de travail de votre application logique s’exécute. Le déclencheur transmet le contenu de la réponse aux actions de votre application logique.

Vous pouvez utiliser l’action HTTP comme toute autre étape de votre flux de travail pour appeler le point de terminaison quand vous le souhaitez. La réponse du point de terminaison détermine la façon dont s’exécutent les actions restantes de votre flux de travail.

En fonction des capacités du point de terminaison cible, le connecteur HTTP prend en charge les versions 1.0, 1.1 et 1.2 du protocole TLS (Transport Layer Security). Logic Apps négocie avec le point de terminaison en utilisant la version prise en charge la plus récente possible. Si, par exemple, le point de terminaison prend en charge la version 1.2, le connecteur commence par utiliser celle-ci. Sinon, le connecteur utilise la version prise en charge juste inférieure.

## <a name="prerequisites"></a>Conditions préalables requises

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* L’URL du point de terminaison cible que vous souhaitez appeler

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md). Si vous débutez avec les applications logiques, voir [Qu’est-ce qu’Azure Logic Apps](../logic-apps/logic-apps-overview.md) ?

* L’application logique à partir de laquelle vous souhaitez appeler le point de terminaison cible. Pour démarrer avec un déclencheur HTTP, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser l’action HTTP, démarrez votre application logique avec le déclencheur de votre choix. Cet exemple utilise le déclencheur HTTP en tant que première étape.

## <a name="add-an-http-trigger"></a>Ajouter un déclencheur HTTP

Ce déclencheur intégré effectue un appel HTTP vers l’URL spécifiée d’un point de terminaison et renvoie une réponse.

1. Connectez-vous au [portail Azure](https://portal.azure.com). Ouvrez votre application logique vide dans le Concepteur d’application logique.

1. Sous **Choisir une action**, dans la zone de recherche, entrez « http » en guise de filtre. Dans la liste **Déclencheurs**, sélectionnez le déclencheur **HTTP**.

   ![Sélectionner le déclencheur HTTP](./media/connectors-native-http/select-http-trigger.png)

   Cet exemple renomme le déclencheur « Déclencheur HTTP » afin que l’étape ait un nom plus descriptif. En outre, il ajoute ultérieurement une action HTTP, et les deux noms doivent être uniques.

1. Indiquez les valeurs des [paramètres du déclencheur HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) à inclure dans l’appel au point de terminaison cible. Configurez la fréquence à laquelle le déclencheur doit vérifier le point de terminaison cible.

   ![Entrer les paramètres du déclencheur HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Si vous sélectionnez un type d’authentification autre que **Aucun**, les paramètres d’authentification varient en fonction de votre sélection. Pour plus d’informations sur les types d’authentification disponibles pour HTTP, consultez ces rubriques :

   * [Ajouter l’authentification aux appels sortants](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Authentifier l’accès aux ressources avec des identités managées](../logic-apps/create-managed-service-identity.md)

1. Pour ajouter d’autres paramètres disponibles, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez les paramètres de votre choix.

1. Continuez à générer le flux de travail de votre application logique avec des actions qui s’exécutent quand le déclencheur se déclenche.

1. Lorsque vous avez terminé, pensez à enregistrer votre application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

## <a name="add-an-http-action"></a>Ajouter une action HTTP

Cette action intégrée effectue un appel HTTP à l’URL spécifiée d’un point de terminaison et renvoie une réponse.

1. Connectez-vous au [portail Azure](https://portal.azure.com). Ouvrez votre application logique dans le Concepteur d’applications logiques.

   Cet exemple utilise le déclencheur HTTP en tant que première étape.

1. Sous l’étape à laquelle vous souhaitez ajouter l’action HTTP, choisissez **Nouvelle étape**.

   Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. Cliquez sur le signe ( **+** ) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Sous **Choisir une action**, dans la zone de recherche, entrez « http » en guise de filtre. Dans la liste **Actions**, sélectionnez **HTTP**.

   ![Sélection de l’action HTTP](./media/connectors-native-http/select-http-action.png)

   Cet exemple renomme l’action « Action HTTP » afin qu’elle ait un nom plus descriptif.

1. Indiquez les valeurs des [paramètres d’actions HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) à inclure dans l’appel au point de terminaison cible.

   ![Entrer les paramètres de l’action HTTP](./media/connectors-native-http/http-action-parameters.png)

   Si vous sélectionnez un type d’authentification autre que **Aucun**, les paramètres d’authentification varient en fonction de votre sélection. Pour plus d’informations sur les types d’authentification disponibles pour HTTP, consultez ces rubriques :

   * [Ajouter l’authentification aux appels sortants](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Authentifier l’accès aux ressources avec des identités managées](../logic-apps/create-managed-service-identity.md)

1. Pour ajouter d’autres paramètres disponibles, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez les paramètres de votre choix.

1. Lorsque vous avez terminé, pensez à enregistrer votre application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

## <a name="content-with-multipartform-data-type"></a>Contenu avec type multipart/form-data

Pour gérer le contenu qui a le type `multipart/form-data` dans les requêtes HTTP, vous pouvez ajouter un objet JSON qui comprend les attributs `$content-type` et `$multipart` dans le corps de la requête HTTP à l’aide de ce format.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

Par exemple, supposons que vous ayez une application logique qui envoie une requête HTTP POST pour un fichier Excel sur un site web à l’aide de l’API de ce site, qui prend en charge le type `multipart/form-data`. Voici comment cette action peut se présenter :

![Données de formulaire en plusieurs parties](./media/connectors-native-http/http-action-multipart.png)

Voici le même exemple qui montre la définition JSON de l’action HTTP dans la définition de flux de travail sous-jacente :

```json
{
   "HTTP_action": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="connector-reference"></a>Référence de connecteur

Pour en savoir plus sur les paramètres des déclencheurs et des actions, consultez les sections suivantes :

* [Paramètres de déclencheurs HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [Paramètres d’actions HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Détails des résultats

Voici d’autres informations sur les sorties d’un déclencheur ou d’une action HTTP qui renvoient les données suivantes :

| Nom de la propriété | Type | Description |
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
