---
title: Corriger les mots mal orthographiés – LUIS
titleSuffix: Azure Cognitive Services
description: Corrigez les mots mal orthographiés dans les énoncés en ajoutant l’API Vérification orthographique Bing v7 aux requêtes de point de terminaison LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 43cb2f0c3ef0f845afcd309dccb35bc632ffd324
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390947"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Corriger les mots mal orthographiés avec la vérification orthographique Bing

Vous pouvez intégrer l’[API Vérification orthographique Bing v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) dans votre application LUIS pour corriger les mots mal orthographiés dans les énoncés avant LUIS prédise le score et les entités de l’énoncé. 

## <a name="create-first-key-for-bing-spell-check-v7"></a>Créer la première clé pour la vérification orthographique Bing v7
Votre [première clé d’API Vérification orthographique Bing v7](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) est gratuite. 

![Créer une clé gratuite](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name"create-subscription-key"></a>
## <a name="create-endpoint-key"></a>Créer une clé de point de terminaison
Si votre clé gratuite a expiré, créez une clé de point de terminaison.

1. Connectez-vous au [portail Azure](https://portal.azure.com). 

2. Dans le coin supérieur gauche, sélectionnez **Créer une ressource**.

3. Dans la zone de recherche, entrez `Bing Spell Check API V7`.

    ![Rechercher l’API Vérification orthographique Bing v7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Sélectionnez le service. 

5. Un panneau d’informations apparaît à droite, avec notamment les mentions légales. Sélectionnez **Créer** pour commencer le processus de création d’abonnement. 

6. Dans le panneau suivant, entrez les paramètres du service. Attendez la fin du processus de création de service.

    ![Entrer les paramètres du service](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Sélectionnez **Toutes les ressources** sous le titre **Favoris** dans la barre de navigation à gauche.

8. Sélectionnez le nouveau service. Son type est **Cognitive Services** et l’emplacement est **global**. 

9. Dans le panneau principal, sélectionnez **Clés** pour voir vos nouvelles clés.

    ![Obtenir les clés](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Copiez la première clé. Vous avez seulement besoin d’une des deux clés. 

## <a name="using-the-key-in-luis-test-panel"></a>Utilisation de la clé dans le panneau de test LUIS
Il existe deux emplacements dans LUIS pour utiliser la clé. Le premier est le [panneau de test](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). La clé n’est pas enregistrée dans LUIS, mais c’est une variable de session. Vous devez définir la clé chaque fois que le panneau de test doit appliquer le service de l’API Vérification orthographique Bing v7 à l’énoncé. Consultez les [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) de paramétrage de la clé dans le panneau de test.

## <a name="adding-the-key-to-the-endpoint-url"></a>Ajout de la clé à l’URL de point de terminaison
La requête de point de terminaison a besoin de la clé transmise dans les paramètres de chaîne de requête pour chaque requête à laquelle vous souhaitez appliquer la correction orthographique. Vous pouvez avoir un chatbot qui appelle LUIS, ou vous pouvez appeler l’API de point de terminaison LUIS directement. Quelle que soit la façon dont le point de terminaison est appelé, chaque appel doit inclure les informations requises pour que les corrections orthographiques fonctionnent correctement.

L’URL de point de terminaison a plusieurs valeurs qui doivent être transmises correctement. La clé d’API Vérification orthographique Bing v7 en fait partie. Vous devez définir le paramètre **spellCheck** sur la valeur True et configurer la valeur de **bing-spell-check-subscription-key** avec la valeur de clé :

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Envoyer un énoncé mal orthographié à LUIS
1. Dans un navigateur web, copiez la chaîne précédente et remplacez `region`, `appId`, `luisKey` et `bingKey` par vos propres valeurs. Vérifiez que vous utilisez la région du point de terminaison, si elle est différente de votre [région](luis-reference-regions.md) de publication.

2. Ajoutez un énoncé mal orthographié, comme « How far is the mountainn ? ». En anglais, `mountain`, avec un `n`, est l’orthographe correcte. 

3. Appuyez sur Entrée pour envoyer la requête à LUIS.

4. LUIS répond avec un résultat JSON pour `How far is the mountain?`. Si l’API Vérification orthographique Bing v7 détecte une faute d’orthographe, le champ `query` de la réponse JSON de l’application LUIS contient la requête d’origine, et le champ `alteredQuery` contient la requête corrigée envoyée à LUIS.

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
Si vous ne souhaitez pas utiliser le service d’API Vérification orthographique Bing v7, vous pouvez étiqueter les énoncés qui présentent des fautes d’orthographe afin que LUIS puisse apprendre orthographe adéquate, ainsi que les fautes de frappe. Cette option requiert davantage d’efforts en matière d’étiquetage que l’utilisation d’un vérificateur d’orthographe.

## <a name="publishing-page"></a>Page de publication
La page de [publication](luis-how-to-publish-app.md) comporte une case à cocher permettant d’**activer la vérification orthographique Bing**. Cela est pratique pour créer la clé et de comprendre comment l’URL de point de terminaison change. Vous devez toujours utiliser les paramètres de point de terminaison appropriés afin de disposer de l’orthographe corrigée pour chaque énoncé. 

> [!div class="nextstepaction"]
> [En savoir plus sur les exemples d’énoncés](luis-how-to-add-example-utterances.md)
