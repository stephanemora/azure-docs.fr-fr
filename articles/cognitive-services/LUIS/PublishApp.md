---
title: Publier votre application LUIS | Microsoft Docs
description: Après avoir créé et testé votre application à l’aide de Language Understanding (LUIS), publiez-la en tant que un service web sur Azure.
services: cognitive-services
titleSuffix: Azure
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 1bd24af0498755b7cdcb170624fd8f9f3b39c85a
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341287"
---
# <a name="publish-your-trained-app"></a>Publier votre application formée
Lorsque vous avez fini de créer et tester votre application LUIS, publiez-la. Une fois l’application publiée, la page Publier affiche tous les [points de terminaison](luis-glossary.md#endpoint) HTTP associés. Ces points de terminaison sont ensuite intégrés, par [région](luis-reference-regions.md) et par [clé](luis-how-to-manage-keys.md), dans une application cliente, de chatbot ou principale. 

Vous pouvez toujours [tester](interactive-test.md) votre application avant de la publier. 

## <a name="production-and-staging-slots"></a>Emplacements de préproduction et de production
Vous pouvez publier votre application dans l’**emplacement de préproduction** ou dans l’**emplacement de production**. L’utilisation de deux emplacements de publication vous permet d’avoir deux versions différentes avec des points de terminaison publiés, ou la même version sur deux points de terminaison différents. 

<!-- TBD: what is the technical difference? log files, endpoint quota? -->

## <a name="settings-configuration-requires-publishing-model"></a>La configuration des paramètres nécessite un modèle de publication
Publiez sur le point de terminaison après modification des paramètres suivants. 

## <a name="external-services-settings"></a>Paramètres des services externes
Les paramètres des services externes sont l’**[analyse des sentiments](#enable-sentiment-analysis)** et la **[préparation vocale](#enable-speech-priming)**.

### <a name="enable-sentiment-analysis"></a>Activer l’analyse des sentiments
Dans les **Paramètres des services externes**, la case à cocher **Enable Sentiment Analysis** (Activer l’analyse des sentiments) permet à LUIS de s’intégrer avec l’[Analyse de texte](https://azure.microsoft.com/services/cognitive-services/text-analytics/) pour fournir une analyse des sentiments et des phrases clés. Vous ne devez pas nécessairement fournir une clé d’Analyse de texte, et votre compte Azure ne sera pas facturé pour ce service. Lorsque vous activez ce paramètre, il est persistant. 

Les données de sentiment sont un score compris de 1 à 0 indiquant le sentiment positif (plus proche de 1) ou négatif (plus proche de 0) reflétant les données.

Pour plus d’informations sur la réponse du point de terminaison JSON avec l’analyse des sentiments, voir [Analyse des sentiments](luis-concept-data-extraction.md#sentiment-analysis).

### <a name="enable-speech-priming"></a>Activer la préparation vocale 
Dans les **Paramètres des services externes**, la case à cocher **Enable Speech Priming** (Activer la préparation vocale) vous permet d’avoir un point de terminaison unique pour obtenir un énoncé parlé d’un chatbot ou d’une application appelante de LUIS, et de recevoir une réponse de prédiction de LUIS. La préparation vocale utilise le service cognitif [API Microsoft Speech](../Speech-Service/rest-apis.md). 

![Image de la boîte de dialogue de confirmation de la préparation vocale](./media/luis-how-to-publish-app/speech-prime-modal.png)

Une fois cette fonctionnalité activée, publiez votre application. Lorsque vous publiez votre application LUIS, votre modèle d’application est envoyé à votre propre service Speech pour préparer celui-ci. Les informations de votre modèle ne sont **pas** utilisées en dehors de votre propre service. 

Pour utiliser la préparation vocale, vous avez besoin des informations suivantes à utiliser dans le [Kit de développement logiciel (SDK)](../speech-service/speech-sdk-reference.md) :
* Une clé de point de terminaison LUIS.
* L’ID d’application LUIS.
* Un domaine de point de terminaison, appelé « Hostname » dans le Kit de développement logiciel Speech, tel que « westus.api.cognitive.microsoft.com », où le premier sous-domaine indique la région où l’application est publiée.

Pour plus d’informations, voir l’exemple de [conversion de sortie orale en intention](http://aka.ms/speechsdk).

Lorsque votre application LUIS ou le service Speech sont supprimés, les données de votre modèle le sont également. 

## <a name="endpoint-url-settings"></a>Paramètres d’URL de point de terminaison
Les paramètres des services d’URL de point de terminaison sont **[Timezone](#set-timezone-offset)** offset (Décalage des fuseaux horaires), **[All predicted intent scores](#include-all-predicted-intent-scores)** (Tous les scores d’intention prédits) et **[Bing spell checker](#enable-bing-spell-checker)** (Vérification orthographique Bing).

### <a name="set-timezone-offset"></a>Définir le décalage des fuseaux horaires 
Un aspect du choix de l’emplacement est la sélection de fuseau horaire. Ce paramètre de fuseau horaire permet à LUIS de [modifier](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) toute valeur d’heure datetimev2 prédéfinie pendant la prédiction, de façon à ce que les données d’entité renvoyées soient correctes compte tenu du fuseau horaire sélectionné. 

### <a name="include-all-predicted-intent-scores"></a>Inclure tous les scores d’intention prédits
La case à cocher **Include all predicted intent scores** (Inclure tous les scores d’intention prédits) permet de choisir que la réponse à la requête de point de terminaison inclue le score de prédiction pour chaque intention. 

Ce paramètre permet à votre chatbot ou à application appelante de LUIS de prendre une décision par programmation basée sur les scores des intentions renvoyées. En règle générale, les deux premières intentions sont les plus intéressantes. Si le score supérieur est l’intention None, votre chatbot peut choisir de poser une question de suivi qui effectue un choix définitif entre l’intention None et l’autre intention de score élevé. 

Les intentions et leurs scores sont également inclus dans les journaux d’activité de point de terminaison. Vous pouvez [exporter](create-new-app.md#export-app) ces journaux d’activité et analyser les scores. 

```
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

### <a name="enable-bing-spell-checker"></a>Activer le vérificateur orthographique Bing 
Dans les **Paramètres d’URL de point de terminaison**, la case à cocher **Enable Bing spell checker** (Activer le vérificateur orthographique Bing) permet à LUIS de corriger les mots mal orthographiés avant la prédiction. Cela vous oblige à créer une  **[clé de vérification orthographique Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. Une fois la clé créée, les deux paramètres QueryString sont ajoutés à l’URL de point de terminaison sur la page de publication. 

Si vous construisez vos propres URL pour votre application appelante de LUIS, vérifiez le paramètre QueryString **spellCheck=true** et **bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}**. Remplacez `{YOUR_BING_KEY_HERE}` par le clé de votre vérificateur orthographique Bing.

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

## <a name="publish-your-trained-app-to-an-http-endpoint"></a>Publier votre application formée sur un point de terminaison HTTP
Ouvrez votre application en cliquant sur son nom dans la page **My Apps** (Mes applications), puis cliquez sur **Publier** dans le volet supérieur. 

![Page Publier](./media/luis-how-to-publish-app/publish-to-production.png)
 
Une fois votre application correctement publiée, une notification de réussite de couleur verte s’affiche en haut du navigateur. 

* Indiquez si vous souhaitez publier en **Production** ou en **Préproduction** en opérant une sélection dans le menu déroulant sous **Sélectionner un emplacement**. 

## <a name="assign-key"></a>Affecter une clé

Si vous souhaitez utiliser une clé autre que la clé gratuite Starter_Key indiquée, cliquez sur le bouton **Ajouter une clé**. Cette action ouvre une boîte de dialogue qui vous permet de sélectionner une clé de point de terminaison existante à assigner à l’application. Pour plus d’informations sur la création et l’ajout de clés de point de terminaison à votre application LUIS, voir [Gérer vos clés](luis-how-to-manage-keys.md).

Pour afficher les points de terminaison et les clés associés à d’autres régions, utilisez les cases d’option pour le changement de région. Chaque ligne du tableau **Ressources et clés** répertorie les ressources Azure associées à votre compte et les clés de point de terminaison associées à ces ressources.

## <a name="endpoint-url-construction"></a>Construction d’URL de point de terminaison
L’URL de point de terminaison correspond à la région Azure associée à la clé de point de terminaison.

Ce tableau reflète commodément votre configuration de publication dans le point de terminaison d’URL avec les choix d’itinéraire et les valeurs de chaîne de requête. Si vous construisez vos URL de point de terminaison pour votre application appelante de LUIS, assurez-vous que ces mêmes itinéraires et valeurs de chaîne de requête sont définis pour le point de terminaison utilisé (si vous souhaitez les définir).

L’itinéraire d’URL est construit avec la région et l’ID d’application. Si vous publiez dans d’autres régions ou avec d’autres applications, l’URL de point de terminaison peut être construite en modifiant les valeurs d’ID de région et d’application. 

* Sélectionnez l’emplacement de Production, puis le bouton **Publier**. Une fois la publication réussie, utilisez l’URL de point de terminaison affichée pour accéder à votre application LUIS. 

### <a name="optional-query-string-parameters"></a>Paramètres facultatifs de chaîne de requête
Les paramètres de chaîne de requête suivants peuvent être utilisés avec l’URL de point de terminaison :

<!-- TBD: what about speech priming? -->

|Chaîne de requête|type|Exemple de valeur|Objectif|
|--|--|--|--|
|verbose|booléenne|true|Inclure [tous les scores d’intention](#include-all-predicted-intent-scores) pour un énoncé|
|timezoneOffset|nombre (de minutes)|60|Définir le [décalage de fuseau horaire](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) pour [les entités datetimev2 prédéfinies](luis-reference-prebuilt-datetimev2.md)|
|spellCheck|booléenne|true|[Corriger l’orthographe](#enable-bing-spell-checker) de l’énoncé ; utilisée conjointement avec le paramètre de chaîne de requête bing-spell-check-subscription-key|
|bing-spell-check-subscription-key|ID d’abonnement||utilisée conjointement avec le paramètre de chaîne de requête spellCheck|
|staging|booléenne|false|sélectionner un point de terminaison de production ou de préproduction|
|log|booléenne|true|ajouter une requête et des résultats au journal|


## <a name="test-your-published-endpoint-in-a-browser"></a>Tester votre point de terminaison publié dans un navigateur
Testez votre point de terminaison publié en sélectionnant l’URL dans la colonne **Point de terminaison**. Le navigateur par défaut s’ouvre avec l’URL générée. Définir le paramètre d’URL « &q » sur votre requête de test. Par exemple, ajoutez `&q=Book me a flight to Boston on May 4` à votre URL, puis appuyez sur Entrée. Le navigateur affiche la réponse JSON de votre point de terminaison HTTP. 

![Réponse JSON d’un point de terminaison HTTP publié](./media/luis-how-to-publish-app/luis-publish-app-json-response.png)

## <a name="next-steps"></a>Étapes suivantes

* Voir [Gérer les clés](./luis-how-to-manage-keys.md) pour ajouter des clés à votre application LUIS et découvrir comment les clés sont mappées aux régions.
* Voir [Former et tester votre application](interactive-test.md) pour obtenir des instructions sur la façon de tester votre application publiée dans la console de test.
