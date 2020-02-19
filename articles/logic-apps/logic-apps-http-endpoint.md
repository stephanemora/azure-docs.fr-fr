---
title: Appeler, déclencher ou imbriquer des applications logiques
description: Configurer des points de terminaison HTTP pour appeler, déclencher ou imbriquer des workflows d’application logique dans Azure Logic Apps
services: logic-apps
ms.workload: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: d5b5a69c7927d07c0ae6b3b56ec97b6551e5d46b
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191328"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>Appeler, déclencher ou imbriquer des applications logiques à l’aide de points de terminaison HTTP dans Azure Logic Apps

Afin que votre application logique puisse être appelée via une URL pour qu’elle puisse recevoir des demandes entrantes d’autres services, vous pouvez exposer en mode natif un point de terminaison HTTP synchrone en tant que déclencheur sur cette application logique. Quand vous configurez cette fonctionnalité, vous pouvez également imbriquer votre application logique dans d’autres applications logiques, ce qui vous permet de créer un modèle de points de terminaison pouvant être appelés.

Pour configurer un point de terminaison HTTP, vous pouvez utiliser l’un de ces types de déclencheurs, qui permettent aux applications logiques de recevoir des demandes entrantes :

* [Requête](../connectors/connectors-native-reqres.md)
* [Déclencheur HTTPWebhook](../connectors/connectors-native-webhook.md)
* Les déclencheurs de connecteur managé ayant le [type ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) et pouvant recevoir des requêtes HTTP entrantes

> [!NOTE]
> Ces exemples utilisent le déclencheur de requête, mais vous pouvez utiliser n’importe quel déclencheur basé sur une requête HTTP figurant dans la liste précédente. Tous les principes s’appliquent de manière identique à ces autres types de déclencheurs.

Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps](../logic-apps/logic-apps-overview.md) et [Démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Conditions préalables requises

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/).

* L’application logique dans laquelle vous souhaitez configurer le point de terminaison HTTP comme déclencheur. Vous pouvez commencer avec une application logique vide ou une application logique existante au sein de laquelle vous souhaitez remplacer le déclencheur actuel. Pour cet exemple, vous avez besoin d’une application logique vide.

## <a name="create-a-callable-endpoint"></a>Créer un point de terminaison pouvant être appelé

1. Connectez-vous au [portail Azure](https://portal.azure.com). Créez et ouvrez une application logique vide dans le concepteur d’applications logiques.

   Cet exemple utilise le déclencheur de requête, mais vous pouvez utiliser n’importe quel déclencheur capable de recevoir des requêtes HTTP entrantes. Tous les principes s’appliquent de manière identique à ces déclencheurs. Pour plus d’informations sur le déclencheur de requête, consultez [Recevoir et répondre aux appels HTTPS entrants à l’aide d’Azure Logic Apps](../connectors/connectors-native-reqres.md).

1. Dans la zone de recherche, sélectionnez **Intégré**. Dans la zone de recherche, entrez `request` en guise de filtre. Dans la liste Déclencheurs, sélectionnez **Lors de la réception d’une requête HTTP**.

   ![Localisez et sélectionnez le déclencheur Requête](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. Si vous le souhaitez, dans la boîte du **schéma JSON du corps de la requête**, vous pouvez entrer un schéma JSON qui décrit la charge utile ou les données que le déclencheur doit recevoir.

   Le concepteur utilise ce schéma pour générer des jetons qui représentent des sorties de déclencheur. Vous pouvez ensuite facilement référencer ces sorties dans le flux de travail de votre application logique. Cliquez sur le lien renvoyant à la section [Jetons générés à partir de schémas JSON pour votre application logique](#generated-tokens) pour en apprendre davantage.

   Pour cet exemple, entrez ce schéma :

   ```json
      {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "streetNumber": {
                  "type": "string"
               },
               "streetName": {
                  "type": "string"
               },
               "town": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "string"
               }
            }
         }
      }
   }
    ```

   ![Fournir le schéma JSON pour l’action de requête](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   Ou vous pouvez générer un schéma JSON en fournissant un exemple de charge utile :

   1. Dans le déclencheur de **requête**, sélectionnez **Utiliser l’exemple de charge utile pour générer le schéma**.

   1. Dans la boîte **Entrer ou coller un exemple de charge utile JSON**, entrez votre exemple de charge utile, par exemple :

      ```json
      {
         "address": {
            "streetNumber": "00000",
            "streetName": "AnyStreet",
            "town": "AnyTown",
            "postalCode": "11111-1111"
        }
      }
      ```

   1. Quand vous êtes prêt, sélectionnez **Terminé**.

      La boîte de **Schéma JSON du corps de la requête** affiche maintenant le schéma généré.

1. Enregistrez votre application logique.

   La boîte **POST HTTP pour cette URL** affiche désormais l’URL de rappel générée que les autres services peuvent utiliser pour appeler et déclencher votre application logique. Cette URL contient une clé de signature d’accès partagé (SAP), qui est utilisée pour l’authentification dans les paramètres de requête, par exemple :

   ![URL de rappel générée pour le point de terminaison](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   Vous pouvez également récupérer l’URL du point de terminaison HTTP à partir du volet **Vue d’ensemble** de l’application logique.

   1. Dans le menu de votre application logique, sélectionnez **Vue d’ensemble**.

   1. Dans la section **Résumé**, sélectionnez **Afficher l’historique du déclencheur**.

      ![Obtenir l’URL de point de terminaison HTTP à partir du portail Azure](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   1. Sous **URL de rappel [POST]** , copiez l’URL :

      ![Copier l’URL de point de terminaison HTTP à partir du Portail Azure](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url.png)

      Ou vous pouvez obtenir l’URL en appelant :

      ```http
      POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01
      ```

<a name="set-method"></a>

## <a name="set-expected-http-method"></a>Définir la méthode HTTP attendue

Par défaut, le déclencheur Requête attend une requête HTTP POST. Toutefois, vous pouvez spécifier une méthode différente à attendre, mais une seule méthode.

1. Dans le déclencheur de requête, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez **Méthode** qui ajoute cette propriété au déclencheur.

   ![Ajouter la propriété « Méthode » au déclencheur](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. Dans la liste **Méthode**, sélectionnez une autre méthode attendue par le déclencheur. Ou vous pouvez spécifier une méthode personnalisée.

   Par exemple, sélectionnez la méthode **GET** pour pouvoir tester ultérieurement l’URL de votre point de terminaison HTTP.

   ![Sélectionner la méthode HTTP à utiliser pour le déclencheur](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-in-endpoint-url"></a>Accepter les paramètres dans l’URL du point de terminaison

Si vous souhaitez que votre URL de point de terminaison accepte des paramètres, spécifiez le chemin d’accès relatif dans votre déclencheur. Vous devez également [définir explicitement la méthode](#set-method) attendue par votre requête HTTP.

1. Dans le déclencheur de requête, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez **Chemin d’accès relatif** qui ajoute cette propriété au déclencheur.

   ![Ajouter la propriété « Chemin d’accès relatif » au déclencheur](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. Dans la propriété **Chemin d’accès relatif**, spécifiez le chemin d’accès relatif que vous souhaitez que votre URL accepte pour le paramètre dans votre schéma JSON, par exemple `address/{postalCode}`.

   ![Spécifier le chemin d’accès relatif pour le paramètre](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. Pour utiliser le paramètre, recherchez et ajoutez une action **Réponse** à votre application logique.

   1. Sous le déclencheur de requête, sélectionnez **Nouvelle étape** > **Ajouter une action**.

   1. Sous **Choisir une action**, dans la zone de recherche, entrez `response` en guise de filtre.

   1. Dans la liste des actions, sélectionnez l’action **Réponse**.

1. Dans la propriété de **corps** de l’action de réponse, incluez le jeton qui représente le paramètre que vous avez spécifié dans le chemin d’accès relatif de votre déclencheur.

   Par exemple, supposons que vous souhaitiez que l’action de réponse retourne `Postal Code: {postalCode}`.

   Dans la propriété de **corps**, entrez `Postal Code: ` avec un espace de fin. Dans la liste de contenu dynamique qui s’affiche, sélectionnez la propriété **postalCode**.

   ![Ajouter le paramètre spécifié au corps de la réponse](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   La propriété de **corps** contient désormais le paramètre sélectionné :

   ![Exemple de corps de la réponse avec le paramètre](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Enregistrez votre application logique.

    Votre URL de point de terminaison HTTP inclut désormais le chemin d’accès relatif, par exemple :

    ```http
    https://prod-25.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}
    ```

1. Pour tester votre point de terminaison HTTP, copiez et collez l’URL mise à jour dans une autre fenêtre de navigateur, mais remplacez `{postalCode}` par `123456`, puis appuyez sur ENTRÉE.

   Votre navigateur affiche ce texte : `Postal Code: 123456`

## <a name="call-logic-app-through-http-endpoint"></a>Appeler une application logique via un point de terminaison HTTP

Après avoir créé le point de terminaison HTTP, vous pouvez déclencher l’application logique en envoyant une requête HTTP `POST` à l’URL complète du point de terminaison. Les applications logiques ont une prise en charge intégrée pour les points de terminaison à accès direct.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Jetons générés à partir du schéma

Lorsque vous fournissez un schéma JSON dans le déclencheur de requête, le concepteur d’application logique génère des jetons pour les propriétés de ce schéma. Vous pouvez ensuite utiliser ces jetons pour transmettre des données au workflow de votre application logique.

Par exemple, si vous ajoutez d’autres propriétés, telles que `"suite"`, à votre schéma JSON, vous pouvez utiliser des jetons pour ces propriétés dans les étapes ultérieures de votre application logique. Voici le schéma JSON complet :

```json
   {
   "type": "object",
   "properties": {
      "address": {
         "type": "object",
         "properties": {
            "streetNumber": {
               "type": "string"
            },
            "streetName": {
               "type": "string"
            },
            "suite": {
               "type": "string"
            },
            "town": {
               "type": "string"
            },
            "postalCode": {
               "type": "string"
            }
         }
      }
   }
}
```

## <a name="create-nested-logic-apps"></a>Créer des applications logiques imbriquées

Vous pouvez imbriquer des workflows dans votre application logique en ajoutant d’autres applications logiques qui peuvent recevoir des requêtes. Pour inclure ces applications logiques, procédez comme suit :

1. Sous l’étape lors de laquelle vous souhaitez appeler une autre application logique, sélectionnez **Nouvelle étape** > **Ajouter une action**.

1. Sous **Choisir une action**, sélectionnez **Intégré**. Dans la zone de recherche, entrez `logic apps` en guise de filtre. Dans la liste actions, sélectionnez **Choisir un workflow Logic Apps**.

   ![Imbriquer une application logique à l’intérieur de l’application logique actuelle](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   Le concepteur affiche les applications logiques éligibles parmi lesquelles vous pouvez choisir.

1. Sélectionnez l’application logique à appeler à partir de votre application logique actuelle.

   ![Sélectionner une application logique à appeler à partir de l’application logique actuelle](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Référencer le contenu à partir d’une requête entrante

Si le type de contenu de la requête entrante est `application/json`, vous pouvez référencer les propriétés dans la requête entrante. Sinon, ce contenu est traité comme une seule unité binaire que vous pouvez transmettre à d’autres API. Pour référencer ce contenu dans le flux de travail de votre application logique, vous devez d’abord convertir ce contenu.

Par exemple, si vous transmettez du contenu dont le type de `application/xml` est, vous pouvez utiliser l’expression [`@xpath()`](../logic-apps/workflow-definition-language-functions-reference.md#xpath) pour effectuer une extraction XPath ou utiliser l’expression [`@json()`](../logic-apps/workflow-definition-language-functions-reference.md#json) pour la conversion de XML en JSON. En savoir plus sur l’utilisation des [types de contenus](../logic-apps/logic-apps-content-type.md) pris en charge.

Pour obtenir la sortie à partir d’une requête entrante, vous pouvez utiliser l’expression [`@triggerOutputs`](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs). Par exemple, supposons que vous ayez une sortie ressemblant à l’exemple suivant :

```json
{
   "headers": {
      "content-type" : "application/json"
   },
   "body": {
      "myProperty" : "property value"
   }
}
```

Pour accéder spécifiquement à la propriété `body`, vous pouvez utiliser l’expression [`@triggerBody()`](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) comme raccourci.

## <a name="respond-to-requests"></a>Répondre aux requêtes

Parfois, vous souhaitez répondre à certaines demandes qui déclenchent votre application logique en renvoyant le contenu à l’appelant. Pour créer le code d’état, l’en-tête et le corps de votre réponse, utilisez l’action Réponse. Cette action peut apparaître n’importe où dans votre application logique, et pas seulement à la fin de votre workflow. Si votre application logique n’inclut pas d’action Réponse, le point de terminaison HTTP répond *immédiatement* avec un état **202 - Accepté**.

Pour que l’appelant d’origine obtienne la réponse, toutes les étapes nécessaires pour la réponse doivent être terminées avant la [limite du délai d’expiration de la requête](./logic-apps-limits-and-config.md), sauf si l’application logique déclenchée est appelée en tant qu’application logique imbriquée. Si aucune réponse n’est retournée avant cette limite, la requête entrante expire et reçoit la réponse **408 - Dépassement du délai d’expiration par le client**.

Pour les applications logiques imbriquées, l’application logique parente continue à attendre une réponse jusqu’à ce que toutes les étapes aient été effectuées, quelle que soit la durée de l’opération.

### <a name="construct-the-response"></a>Construire la réponse

Vous pouvez inclure plusieurs en-têtes et n’importe quel type de contenu dans le corps de la réponse. Par exemple, l’en-tête de cette réponse spécifie que le type de contenu de la réponse est `application/json` et que le corps contient des valeurs pour les propriétés `town` et `postalCode`, en fonction du schéma JSON décrit précédemment dans cette rubrique pour le déclencheur de requête.

![Fournir le contenu de la réponse pour l’action de réponse HTTP](./media/logic-apps-http-endpoint/content-for-response-action.png)

Les réponses ont ces propriétés :

| Propriété (affichage) | Propriété (JSON) | Description |
|--------------------|-----------------|-------------|
| **Code d’état** | `statusCode` | Le code d’état HTTP à utiliser dans la réponse pour la requête entrante. Ce code peut être tout code d’état valide commençant par 2xx, 4xx ou 5xx. Cependant, les codes d’état 3xx ne sont pas autorisés. |
| **En-têtes** | `headers` | Un ou plusieurs en-têtes à inclure dans la réponse |
| **Corps** | `body` | Un objet corps peut être une chaîne, un objet JSON ou même du contenu binaire référencé à partir d’une étape précédente |
||||

Pour afficher la définition JSON de l’action de réponse et de la définition JSON complète de votre application logique, sélectionnez **Mode Code** dans la barre d’outils du concepteur d’applications logiques.

``` json
"Response": {
   "type": "Response",
   "kind": "http",
   "inputs": {
      "body": {
         "postalCode": "@triggerBody()?['address']?['postalCode']",
         "town": "@triggerBody()?['address']?['town']"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

## <a name="q--a"></a>Questions et réponses

#### <a name="q-what-about-url-security"></a>Q : Qu’en est-il de la sécurité de l’URL ?

**R** : Les URL de rappel de l’application logique sont générées de façon sécurisée par Azure à l’aide d’une [signature d’accès partagé (SAP)](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature). Cette signature est transmise directement comme paramètre de requête et doit être validée avant que votre application logique puisse être exécutée. Azure génère cette signature via la combinaison unique d’une clé secrète par application logique, du nom du déclencheur et de l’opération qui est effectuée. Ainsi, à moins que quelqu’un ait accès à la clé secrète de l’application logique, personne ne peut générer de signature valide.

> [!IMPORTANT]
> Pour les systèmes de production et les systèmes ayant un niveau de sécurité supérieur, nous vous déconseillons fortement d’appeler votre application logique directement à partir du navigateur pour les raisons suivantes :
>
> * la clé d’accès partagé s’affiche dans l’URL ;
> * Vous ne pouvez pas gérer les stratégies de contenu de sécurité en raison du partage de domaines entre les clients Azure Logic Apps.

#### <a name="q-can-i-configure-http-endpoints-further"></a>Q : Puis-je configurer des points de terminaison HTTP de façon plus approfondie ?

**R** : Oui, les points de terminaison HTTP prennent en charge une configuration plus avancée par le biais de la [Gestion des API Azure](../api-management/api-management-key-concepts.md). Ce service vous offre également la possibilité de gérer toutes vos API de façon systématique, y compris les applications logiques, de configurer les noms de domaines personnalisés, d’utiliser plus de méthodes d’authentification et bien plus encore, comme par exemple :

* [Modification de la méthode de la requête](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Modification des segments d’URL de la requête](../api-management/api-management-transformation-policies.md#RewriteURL)
* Configuration de vos domaines Gestion des API dans le [portail Azure](https://portal.azure.com/)
* Configuration d’une stratégie pour vérifier l’authentification de base

## <a name="next-steps"></a>Étapes suivantes

* [Recevoir et répondre aux appels HTTPS entrants à l’aide d’Azure Logic Apps](../connectors/connectors-native-reqres.md)