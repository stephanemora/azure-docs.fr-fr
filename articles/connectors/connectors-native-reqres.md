---
title: Recevoir des appels HTTPS et y répondre
description: Gérer les requêtes et les événements HTTPS en temps réel à l’aide d’Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewers: klam, logicappspm
ms.topic: conceptual
ms.date: 10/11/2019
tags: connectors
ms.openlocfilehash: 822a6d1cd812ead8e677a66a9b1e47ebdbcf8aea
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030147"
---
# <a name="receive-and-respond-to-incoming-https-calls-by-using-azure-logic-apps"></a>Recevoir et répondre aux appels HTTPS entrants à l’aide d’Azure Logic Apps

Avec [Azure Logic Apps](../logic-apps/logic-apps-overview.md) et le déclencheur de requête intégré ou l’action Réponse, vous pouvez créer des tâches et des workflows automatisés qui reçoivent et répondent aux requêtes HTTP entrantes. Par exemple, vous pouvez appliquer les actions suivantes à votre application logique :

* Recevoir et répondre à une requête HTTP pour des données situées dans une base de données locale.
* Déclencher un flux de travail lorsqu’un événement de webhook externe se produit.
* Recevoir et répondre à un appel HTTPS en provenance d’une autre application logique.

> [!NOTE]
> Le déclencheur de demande prend en charge *uniquement* le protocole TLS (Transport Layer Security) 1.2 pour les appels entrants. Les appels sortants continuent à prendre en charge les protocoles TLS 1.0, 1.1 et 1.2. Si vous constatez des erreurs de liaison SSL, assurez-vous d’utiliser le protocole TLS 1.2. Pour les appels entrants, voici les suites de chiffrement prises en charge :
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="prerequisites"></a>Conditions préalables requises

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/).

* Connaissances de base sur les [applications logiques](../logic-apps/logic-apps-overview.md). Si vous débutez avec les applications logiques, découvrez [comment créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Ajouter un déclencheur de requête

Ce déclencheur intégré crée un point de terminaison HTTPS qui peut être appelé manuellement et peut recevoir *uniquement* des requête HTTPS entrantes. Lorsque cet événement se produit, le déclencheur s’active et exécute l’application logique. Pour plus d’informations sur la définition JSON sous-jacente du déclencheur et sur la façon d’appeler ce dernier, consultez [Type de déclencheur de requête](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) et [Appeler, déclencher ou imbriquer des workflows avec des points de terminaison HTTP dans Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

1. Connectez-vous au [portail Azure](https://portal.azure.com). Créez une application logique vide.

1. Une fois que le concepteur Logic Apps s’ouvre, entrez « requête HTTP » comme filtre dans la zone de recherche. Dans la liste de déclencheurs, sélectionnez le déclencheur **Quand une requête HTTP est reçue**, qui est la première étape du flux de travail de votre application logique.

   ![Sélectionner le déclencheur de requête](./media/connectors-native-reqres/select-request-trigger.png)

   Le déclencheur de requête affiche les propriétés suivantes :

   ![Déclencheur de requête](./media/connectors-native-reqres/request-trigger.png)

   | Nom de la propriété | Nom de la propriété JSON | Obligatoire | Description |
   |---------------|--------------------|----------|-------------|
   | **URL HTTP POST** | {aucune} | Oui | L’URL de point de terminaison générée après l’enregistrement de l’application logique et utilisée pour appeler votre application logique |
   | **Schéma JSON du corps de la demande** | `schema` | Non | Schéma JSON qui décrit les propriétés et les valeurs dans le corps de la demande entrante |
   |||||

1. Dans la zone **Schéma JSON du corps de la demande**, entrez éventuellement un schéma JSON qui décrit le corps de la demande entrante, par exemple :

   ![Exemple de schéma JSON](./media/connectors-native-reqres/provide-json-schema.png)

   Le concepteur utilise ce schéma pour générer des jetons pour les propriétés dans la requête. De cette façon, votre application logique peut analyser, consommer et transmettre les données de la requête via le déclencheur dans votre flux de travail.

   Voici l'exemple de schéma :

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   Lorsque vous entrez un schéma JSON, le concepteur affiche un rappel pour inclure l'en-tête `Content-Type` dans votre demande et définit cette valeur d’en-tête sur `application/json`. Pour plus d’informations, consultez l’article [Gérer les types de contenu](../logic-apps/logic-apps-content-type.md).

   ![Rappel pour inclure l’en-tête « Content-type »](./media/connectors-native-reqres/include-content-type.png)

   Voici à quoi ressemble cet en-tête au format JSON :

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Pour générer un schéma JSON basé sur la charge utile attendue (données), vous pouvez utiliser un outil tel que [JSONSchema.NET](https://jsonschema.net) ou suivre ces étapes :

   1. Dans le déclencheur de requête, sélectionnez **Utiliser l’exemple de charge utile pour générer le schéma**.

      ![Générer un schéma à partir de la charge utile](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Entrez l’exemple de charge utile, puis sélectionnez **Terminé**.

      ![Générer un schéma à partir de la charge utile](./media/connectors-native-reqres/enter-payload.png)

      Voici l'exemple de charge utile :

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": { 
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. Pour spécifier des propriétés supplémentaires, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez les paramètres que vous souhaitez ajouter.

   | Nom de la propriété | Nom de la propriété JSON | Obligatoire | Description |
   |---------------|--------------------|----------|-------------|
   | **Méthode** | `method` | Non | Méthode que les requêtes entrantes doivent utiliser pour appeler l’application logique |
   | **Chemin relatif** | `relativePath` | Non | Chemin relatif pour le paramètre que l’URL de point de terminaison de l’application logique peut accepter |
   |||||

   L’exemple suivant ajoute la propriété **Méthode** :

   ![Ajouter un paramètre de méthode](./media/connectors-native-reqres/add-parameters.png)

   La propriété de **Méthode** apparaît dans le déclencheur afin que vous puissiez sélectionner une méthode dans la liste.

   ![Sélectionner la méthode](./media/connectors-native-reqres/select-method.png)

1. À présent, ajoutez une autre action comme étape suivante dans votre flux de travail. Sous le déclencheur, sélectionnez **Étape suivante** afin de trouver l’action que vous souhaitez ajouter.

   Par exemple, vous pouvez répondre à la demande en [ajoutant une action Réponse](#add-response), que vous pouvez utiliser pour renvoyer une réponse personnalisée et qui est décrite plus loin dans cette rubrique.

   Votre application logique garde la requête entrante ouverte seulement pendant une minute. En supposant que votre flux de travail d’application logique inclue une action Réponse, si l’application logique ne renvoie pas de réponse une fois ce délai écoulé, votre application logique renvoie un `504 GATEWAY TIMEOUT` à l’appelant. Dans le cas contraire, si votre application logique n’inclut pas d’action Réponse, votre application logique renvoie immédiatement une réponse `202 ACCEPTED` à l’appelant.

1. Lorsque vous avez terminé, enregistrez votre application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**. 

   Cette étape génère l’URL à utiliser pour envoyer la requête qui déclenche l’application logique. Pour copier cette URL, sélectionnez l’icône de copie en regard de l’URL.

   ![URL à utiliser pour déclencher votre application logique](./media/connectors-native-reqres/generated-url.png)

1. Pour déclencher votre application logique, envoyez une requête HTTP à l’URL générée. Par exemple, vous pouvez utiliser un outil tel que [Postman](https://www.getpostman.com/).

### <a name="trigger-outputs"></a>Sorties du déclencheur

Voici plus d’informations sur les sorties du déclencheur de requête :

| Nom de la propriété JSON | Type de données | Description |
|--------------------|-----------|-------------|
| `headers` | Object | Objet JSON qui décrit les en-têtes de la requête |
| `body` | Object | Objet JSON qui décrit le contenu du corps de la requête |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Ajouter une action Réponse

Vous pouvez utiliser l’action Réponse pour répondre avec une charge utile (données) à une requête HTTPS entrante, mais uniquement dans une application logique qui est déclenchée par une requête HTTPS. Vous pouvez ajouter l’action Réponse à n’importe quelle phase de votre workflow. Pour plus d’informations sur la définition JSON sous-jacente pour ce déclencheur, consultez [Type d’action de réponse](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action).

Votre application logique garde la requête entrante ouverte seulement pendant une minute. En supposant que votre flux de travail d’application logique inclue une action Réponse, si l’application logique ne renvoie pas de réponse une fois ce délai écoulé, votre application logique renvoie un `504 GATEWAY TIMEOUT` à l’appelant. Dans le cas contraire, si votre application logique n’inclut pas d’action Réponse, votre application logique renvoie immédiatement une réponse `202 ACCEPTED` à l’appelant.

1. Pour le Concepteur d'application logique, sous l’étape où vous souhaitez ajouter l'action Réponse, sélectionnez **Nouvelle étape**.

   Par exemple, avec le déclencheur de requête vu plus tôt :

   ![Ajouter une nouvelle étape](./media/connectors-native-reqres/add-response.png)

   Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. Cliquez sur le signe ( **+** ) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Sous **Choisir une action**, dans la zone de recherche, entrez « réponse » comme filtre, puis sélectionnez l'action **Réponse**.

   ![Sélectionner l’action Réponse](./media/connectors-native-reqres/select-response-action.png)

   Le déclencheur de requête est réduit dans cet exemple pour des raisons de simplicité.

1. Ajoutez toutes les valeurs requises pour le message de réponse. 

   Dans certains champs, cliquer dans leurs zones ouvre la liste de contenu dynamique. Vous pouvez ensuite sélectionner des jetons qui représentent les sorties disponibles à partir des étapes précédentes du workflow. Les propriétés du schéma spécifié dans l’exemple précédent apparaissent désormais dans la liste de contenu dynamique.

   Par exemple, pour la zone **En-têtes**, incluez `Content-Type` comme nom de clé et définissez la valeur de clé sur `application/json` comme indiqué précédemment dans cette rubrique. Pour la zone **Corps**, vous pouvez sélectionner la sortie du corps du déclencheur dans la liste de contenu dynamique.

   ![Détails de l’action Réponse](./media/connectors-native-reqres/response-details.png)

   Pour afficher les en-têtes au format JSON, sélectionnez **Basculer vers la vue texte**.

   ![En-têtes - Basculer vers la vue texte](./media/connectors-native-reqres/switch-to-text-view.png)

   Voici plus d’informations sur les propriétés que vous pouvez définir dans l’action Réponse. 

   | Nom de la propriété | Nom de la propriété JSON | Obligatoire | Description |
   |---------------|--------------------|----------|-------------|
   | **Code d’état** | `statusCode` | Oui | Code d’état à retourner dans la réponse |
   | **En-têtes** | `headers` | Non | Objet JSON qui décrit un ou plusieurs en-têtes à inclure dans la réponse |
   | **Corps** | `body` | Non | Le corps de texte de la réponse |
   |||||

1. Pour spécifier des propriétés supplémentaires, comme un schéma JSON pour le corps de la réponse, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez les paramètres que vous souhaitez ajouter.

1. Lorsque vous avez terminé, enregistrez votre application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**. 

## <a name="next-steps"></a>Étapes suivantes

* [Connecteurs pour Logic Apps](../connectors/apis-list.md)
