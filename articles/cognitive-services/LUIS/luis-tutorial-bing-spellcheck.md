---
title: Corriger les mots mal orthographiés – LUIS
titleSuffix: Azure Cognitive Services
description: Corrigez les mots mal orthographiés dans les énoncés en ajoutant l’API Vérification orthographique Bing v7 aux requêtes de point de terminaison LUIS.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: 509d1dc0b94bdfa9be5185df0bad793f7702eb26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731032"
---
# <a name="correct-misspelled-words-with-bing-resource"></a>Corriger les mots mal orthographiés à l’aide d’une ressource Bing

L’API de prédiction v3 prend désormais en charge l’[API de vérification orthographique Bing](/bing/search-apis/bing-spell-check/overview). Ajoutez la vérification orthographique à votre application en incluant la clé de votre ressource Recherche Bing dans l’en-tête de vos requêtes. Vous pouvez utiliser une ressource Bing existante si vous en possédez déjà une, ou [en créer une](https://portal.azure.com/#create/Microsoft.BingSearch) pour utiliser cette fonctionnalité. 

Exemple de sortie de prédiction pour une requête mal orthographiée :

```json
{
  "query": "bouk me a fliht to kayro",
  "prediction": {
    "alteredQuery": "book me a flight to cairo",
    "topIntent": "book a flight",
    "intents": {
      "book a flight": {
        "score": 0.9480589
      }
      "None": {
        "score": 0.0332136229
      }
    },
    "entities": {}
  }
}
```

Les corrections orthographiques sont effectuées avant la prédiction d’énoncé utilisateur LUIS. Vous pouvez voir toutes les modifications apportées à l’énoncé d’origine, y compris en lien avec l’orthographe, dans la réponse.

## <a name="create-bing-search-resource"></a>Créer une ressource Recherche Bing

Pour créer une ressource Recherche Bing dans le portail Azure, suivez ces instructions :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le coin supérieur gauche, sélectionnez **Créer une ressource**.

3. Dans la zone de recherche, entrez `Bing Search V7`, puis sélectionnez le service.

4. Un panneau d’informations apparaît à droite, avec notamment les mentions légales. Sélectionnez **Créer** pour commencer le processus de création d’abonnement.

> [!div class="mx-imgBorder"]
> ![Ressource sur l’API Vérification orthographique Bing V7](./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png)

5. Dans le panneau suivant, entrez les paramètres du service. Attendez la fin du processus de création de service.

6. Une fois la ressource créée, accédez au panneau **Clés et point de terminaison** sur la gauche. 

7. Copiez l’une des clés à ajouter à l’en-tête de votre demande de prédiction. Vous aurez seulement besoin d’une des deux clés.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="enable-spell-check-from-ui"></a>Activer la vérification orthographique depuis l’IU 
Vous pouvez activer la vérification orthographique pour votre exemple de requête à l’aide du [portail Luis](https://www.luis.ai). Sélectionnez **Gérer** en haut de l’écran, puis **Ressources Azure** dans le volet de navigation de gauche. Une fois que vous avez associé une ressource de prédiction à votre application, sélectionnez **Changer les paramètres de la requête** au bas de la page, puis collez la clé de ressource dans le champ **Activer la vérification orthographique**.
    
   > [!div class="mx-imgBorder"]
   > ![Activer la vérification orthographique](./media/luis-tutorial-bing-spellcheck/spellcheck-query-params.png)


## <a name="adding-the-key-to-the-endpoint-url"></a>Ajout de la clé à l’URL de point de terminaison
Pour chaque requête à laquelle vous souhaitez appliquer la correction orthographique, la requête de point de terminaison a besoin de la clé de la ressource de vérification orthographique Bing transmise dans le paramètre de chaîne de requête. Vous pouvez avoir un chatbot qui appelle LUIS, ou vous pouvez appeler l’API de point de terminaison LUIS directement. Quelle que soit la façon dont le point de terminaison est appelé, chaque appel doit inclure les informations requises dans la demande de l’en-tête pour que les corrections orthographiques fonctionnent correctement. Vous devez définir la valeur **mkt-bing-spell-check-key** sur la valeur de la clé.

|Clé d’en-tête|Valeur de l’en-tête|
|--|--|
|`mkt-bing-spell-check-key`|Clés disponibles dans le panneau **Clés et point de terminaison** de la ressource|

## <a name="send-misspelled-utterance-to-luis"></a>Envoyer un énoncé mal orthographié à LUIS
1. Ajoutez un énoncé mal orthographié à la requête de prédiction à envoyer, par exemple « À quelle distance se trouve la montagnne ? ». En anglais, `mountain`, avec un `n`, est l’orthographe correcte.

2. LUIS répond avec un résultat JSON pour `How far is the mountain?`. Si l’API Vérification orthographique Bing v7 détecte une faute d’orthographe, le champ `query` de la réponse JSON de l’application LUIS contient la requête d’origine, et le champ `alteredQuery` contient la requête corrigée envoyée à LUIS.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Ignorer les fautes d’orthographe

Si vous ne souhaitez pas utiliser le service API Recherche Bing v7, vous devez ajouter l’orthographe correcte et incorrecte.

Pour ce faire, il existe deux solutions :

* Étiquetez les exemples d'énoncés présentant toutes les orthographes différentes pour permettre à LUIS d'apprendre l’orthographe correcte et les fautes de frappe. Cette option requiert davantage d’efforts en matière d’étiquetage que l’utilisation d’un vérificateur d’orthographe.
* Créez une liste d’expressions avec toutes les variantes de mot. Avec cette solution, vous n’êtes pas tenu d’étiqueter les variantes de mot dans les exemples d’énoncés.


> [!div class="nextstepaction"]
> [En savoir plus sur les exemples d’énoncés](./luis-how-to-add-entities.md)