---
title: Gérer les clés de création et de point de terminaison dans LUIS
titleSuffix: Azure Cognitive Services
description: Après avoir créé une clé de point de terminaison LUIS dans le portail Azure, affectez la clé à l’application LUIS et obtenez l’URL de point de terminaison correcte. Utilisez cette URL de point de terminaison pour obtenir des prédictions LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 62081f96e2081833eb705992914899a6764bd792
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033207"
---
# <a name="add-an-azure-luis-resource-to-app"></a>Ajouter une ressource LUIS Azure à l’application

Après avoir créé une ressource LUIS dans le portail Azure, affectez la ressource à l’application LUIS et obtenez l’URL de point de terminaison correcte. Utilisez cette URL de point de terminaison pour obtenir des prédictions LUIS.

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>

## <a name="assign-resource"></a>Affecter une ressource

1. Créez une clé LUIS sur le [portail Azure](https://portal.azure.com). Pour plus d’instructions, consultez [Création d’une clé de point de terminaison à l’aide d’Azure](luis-how-to-azure-subscription.md).
 
2. Sélectionnez **Manage** (Gérer) dans le menu en haut à droite, puis sélectionnez **Keys and endpoints** (Clés et points de terminaison).

    [ ![Page Keys and endpoints](./media/luis-manage-keys/keys-and-endpoints.png) ](./media/luis-manage-keys/keys-and-endpoints.png#lightbox)

3. Pour ajouter la clé LUIS, sélectionnez **Assign Resource +** (Affecter une ressource).

    ![Affecter une ressource à votre application](./media/luis-manage-keys/assign-key.png)

4. Sélectionnez un locataire dans la boîte de dialogue associée à l’adresse e-mail avec laquelle vous vous connectez au site web de LUIS.  

5. Choisissez le **Nom de l’abonnement** associé à la ressource Azure que vous souhaitez ajouter.

6. Sélectionnez le **Nom de la ressource LUIS**. 

7. Sélectionnez **Assign resource** (Affecter une ressource). 

8. Recherchez la nouvelle ligne dans le tableau et copiez l’URL de point de terminaison. Elle est correctement construite pour effectuer une requête HTTP GET au point de terminaison LUIS afin d’obtenir une prédiction. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="unassign-resource"></a>Désaffecter la ressource
Quand vous désaffectez la clé de point de terminaison, elle n’est pas supprimée d’Azure. Elle n’est simplement plus liée à LUIS. 

Quand une clé de point de terminaison est désaffectée, ou non affectée à l’application, toute requête à l’URL de point de terminaison retourne une erreur : `401 This application cannot be accessed with the current subscription`. 

## <a name="include-all-predicted-intent-scores"></a>Inclure tous les scores d’intention prédits
La case à cocher **Include all predicted intent scores** (Inclure tous les scores d’intention prédits) permet de choisir que la réponse à la requête de point de terminaison inclue le score de prédiction pour chaque intention. 

Ce paramètre permet à votre chatbot ou à application appelante de LUIS de prendre une décision par programmation basée sur les scores des intentions renvoyées. En règle générale, les deux premières intentions sont les plus intéressantes. Si le score supérieur est l’intention None, votre chatbot peut choisir de poser une question de suivi qui effectue un choix définitif entre l’intention None et l’autre intention de score élevé. 

Les intentions et leurs scores sont également inclus dans les journaux de point de terminaison. Vous pouvez [exporter](luis-how-to-start-new-app.md#export-app) ces journaux et analyser les scores. 

```JSON
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

## <a name="enable-bing-spell-checker"></a>Activer le vérificateur orthographique Bing 
Dans les paramètres d’URL de point de terminaison (**Endpoint url settings**), le bouton bascule **Bing spell checker** (Vérificateur orthographique Bing) permet à LUIS de corriger les mots mal orthographiés avant la prédiction. Créez une **[clé de Vérification orthographique Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. 

Ajoutez le paramètre de chaîne de requête **spellCheck=true** et **bing-spell-check-subscription-key={VOTRE_CLÉ_BING}** . Remplacez `{YOUR_BING_KEY_HERE}` par le clé de votre vérificateur orthographique Bing.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```


## <a name="publishing-regions"></a>Régions de publication

En savoir plus sur les[régions](luis-reference-regions.md) de publication, notamment la publication en [Europe](luis-reference-regions.md#publishing-to-europe) et en [Australie](luis-reference-regions.md#publishing-to-australia). Les régions de publication sont différentes des régions de création. Créez une application dans la région de création qui correspond à la région de publication souhaitée pour le point de terminaison de requête.

## <a name="next-steps"></a>Étapes suivantes

Utilisez votre clé pour publier votre application sur la page **Publier votre application**. Pour obtenir des instructions sur la publication, consultez [Publier application](luis-how-to-publish-app.md).

Consultez [Clés dans LUIS](luis-concept-keys.md) pour comprendre les principes de création et de clé de point de terminaison de LUIS.