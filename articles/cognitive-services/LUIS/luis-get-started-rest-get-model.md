---
title: 'Démarrage rapide : Obtenir un modèle avec les API REST - LUIS'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide des API REST, vous utilisez le programme cURL pour ajouter des énoncés et entraîner un modèle.
services: cognitive-services
author: roy-har
manager: diberry
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 06/19/2020
ms.author: v-royha
ms.openlocfilehash: 00bc71f76b15455dd8e4f8242c5379e9e97a53f2
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2020
ms.locfileid: "85119544"
---
# <a name="quickstart-use-curl-and-rest-to-change-model"></a>Démarrage rapide : Utiliser cURL et REST pour changer le modèle

Ce guide de démarrage rapide vous montre comment ajouter des exemples d’énoncés à une application de pizza et comment entraîner cette application. Les exemples d’énoncés sont du texte utilisateur conversationnel mappé à une intention. En fournissant des exemples d’énoncés pour les intentions, vous apprenez à l’application LUIS quels types de texte fourni par l’utilisateur appartiennent à quelle intention.

En suivant ce guide de démarrage rapide, vous effectuez trois appels REST à la suite.

- Tout d’abord, vous chargez un lot d’exemples d’énoncés destinés à l’entraînement du modèle de l’application de pizza, en utilisant l’appel [REST Batch add labels](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09).
- Ensuite, vous commencez une session d’entraînement de l’application de pizza, en utilisant l’appel [REST Train application version](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45).
- Enfin, vous obtenez l’état de la session d’entraînement de l’application de pizza, en utilisant l’appel [REST Get version training status](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c46).

[Documentation de référence](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)

## <a name="prerequisites"></a>Prérequis

* Un compte [LUIS](https://www.luis.ai) gratuit.
* Un éditeur de texte, par exemple [Visual Studio Code](https://code.visualstudio.com/).
* Le programme en ligne de commande cURL. Le programme cURL est préinstallé sur macOS, sur la plupart des distributions Linux ainsi que sur Windows 10 build 1803 et ultérieur.

    Si vous devez installer le programme cURL, téléchargez-le à partir de la [page de téléchargement de cURL](https://curl.haxx.se/download.html).

## <a name="create-pizza-app"></a>Créer une application de pizza

[!INCLUDE [Create pizza app](includes/get-started-get-model-create-pizza-app.md)]

## <a name="create-a-json-file-to-train-the-pizza-app"></a>Créer un fichier JSON pour l’entraînement de l’application de pizza

Pour créer un fichier JSON avec trois exemples d’énoncés, enregistrez les données JSON suivantes dans un fichier nommé `ExampleUtterances.JSON` :

```JSON
[
  {
    "text": "order a pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "order a large pepperoni pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 14,
        "endCharIndex": 28
      },
      {
        "entityName": "Size",
        "startCharIndex": 8,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "I want two large pepperoni pizzas on thin crust",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 17,
        "endCharIndex": 32
      },
      {
        "entityName": "Size",
        "startCharIndex": 11,
        "endCharIndex": 15
      },
      {
        "entityName": "Quantity",
        "startCharIndex": 7,
        "endCharIndex": 9
      },
      {
        "entityName": "Crust",
        "startCharIndex": 37,
        "endCharIndex": 46
      }
    ]
  }
]`
```

Le fichier JSON d’exemples d’énoncés a un format spécifique.

Le champ `text` contient le texte de l’exemple d’énoncé. Le champ `intentName` doit correspondre au nom d’une intention existante dans l’application LUIS. Le champ `entityLabels` est obligatoire. Si vous ne souhaitez étiqueter aucune entité, fournissez un tableau vide.

Si le tableau entityLabels n’est pas vide, les éléments `startCharIndex` et `endCharIndex` doivent marquer l’entité concernée dans le champ `entityName`. L’index est basé sur zéro. Si vous commencez ou terminez l’étiquette au niveau d’un espace dans le texte, l’appel d’API pour ajouter les énoncés échoue.

## <a name="add-example-utterances"></a>Ajouter des exemples d’énoncés

1. Pour charger le lot d’exemples d’énoncés, copiez cette commande dans votre éditeur de texte :

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" ^
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" \
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" \
          --data "@ExampleUtterances.JSON"
    ```

    ***

1. Remplacez les valeurs commençant par `***YOUR-` par vos propres valeurs.

    |Information|Objectif|
    |--|--|
    |`***YOUR-AUTHORING-ENDPOINT***`| L’URL de votre point de terminaison de création. par exemple, « https://REPLACE-WITH-YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/ ». Vous avez défini le nom de votre ressource au moment de sa création.|
    |`***YOUR-APP-ID***`| Votre ID d’application LUIS. |
    |`***YOUR-APP-VERSION***`| La version de votre application LUIS. Pour l’application de pizza, indiquez le numéro de version « 0.1 » (sans les guillemets).|
    |`***YOUR-AUTHORING-KEY***`|Votre clé de création (32 caractères).|

    Les ressources et les clés affectées sont visibles dans le portail LUIS, dans la section Gérer de la page **Ressources Azure**. L’ID d’application est indiqué dans la section Gérer de la page **Paramètres de l’application**.

1. Démarrez une invite de commandes (Windows) ou un terminal (macOS et Linux), puis changez les répertoires par le répertoire où vous avez enregistré le fichier `ExampleUtterances.JSON`.

1. Copiez la commande cURL à partir de l’éditeur et collez-la dans l’invite de commandes (Windows) ou le terminal (macOS et Linux). Appuyez sur Entrée pour exécuter la commande.

    Vous devez normalement voir la réponse suivante :

    ```json
    [{"value":{"ExampleId":1255129706,"UtteranceText":"order a pizza"},"hasError":false},{"value":{"ExampleId":1255129707,"UtteranceText":"order a large pepperoni pizza"},"hasError":false},{"value":{"ExampleId":1255129708,"UtteranceText":"i want two large pepperoni pizzas on thin crust"},"hasError":false}]
    ```

    Voici la sortie mise en forme pour être lisible :

    ```json
    [
      {
        "value": {
          "ExampleId": 1255129706,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129707,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129708,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    ```

## <a name="train-the-pizza-app-model"></a>Entraîner le modèle de l’application de pizza

1. Pour commencer une session d’entraînement de l’application de pizza, copiez cette commande dans votre éditeur de texte :

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" ^
          --data "" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    ***

1. Comme vous l’avez fait avant, remplacez les valeurs commençant par `***YOUR-` par vos propres valeurs.

1. Copiez la commande cURL à partir de l’éditeur et collez-la dans l’invite de commandes (Windows) ou le terminal (macOS et Linux). Appuyez sur Entrée pour exécuter la commande.

    Vous devez normalement voir la réponse suivante :

    ```json
    {"statusId":2,"status":"UpToDate"}
    ```

    Voici la sortie mise en forme pour être lisible :

    ```json
    {
      "statusId": 2,
      "status": "UpToDate"
    }
    ```

## <a name="get-the-status-of-training"></a>Obtenir l’état de l’entraînement

1. Pour obtenir l’état de l’entraînement durant la session d’entraînement, copiez cette commande dans votre éditeur de texte :

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" ^
            --request GET ^
            --header "Content-Type:application/json" ^
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    ***

1. Comme vous l’avez fait avant, remplacez les valeurs commençant par `***YOUR-` par vos propres valeurs.

1. Copiez la commande cURL à partir de l’éditeur et collez-la dans l’invite de commandes (Windows) ou le terminal (macOS et Linux). Appuyez sur Entrée pour exécuter la commande.

    Vous devez normalement voir la réponse suivante :

    ```json
    [{"modelId":"8eb7ad8f-5db5-4c28-819b-ca3905fffd80","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6f53bc92-ae54-44ce-bc4e-010d1f8cfda0","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6cb17888-ad6e-464c-82c0-d37fd1f2c4f8","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"a16fc4fd-1949-4e77-9de3-30369f16c3a5","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6bacdb75-1889-4f93-8971-8c8995ff8960","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"be963f4c-4898-48d7-9eba-3c6af0867b9d","details":{"statusId":2,"status":"UpToDate","exampleCount":171}}]
    ```

    Voici la sortie mise en forme pour être lisible :

    ```json
    [
      {
        "modelId": "8eb7ad8f-5db5-4c28-819b-ca3905fffd80",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6f53bc92-ae54-44ce-bc4e-010d1f8cfda0",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6cb17888-ad6e-464c-82c0-d37fd1f2c4f8",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "a16fc4fd-1949-4e77-9de3-30369f16c3a5",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6bacdb75-1889-4f93-8971-8c8995ff8960",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "be963f4c-4898-48d7-9eba-3c6af0867b9d",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      }
    ]
    ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous avez terminé ce guide de démarrage rapide, supprimez le fichier `ExampleUtterances.JSON` du système de fichiers.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Meilleures pratiques pour une application](luis-concept-best-practices.md)
