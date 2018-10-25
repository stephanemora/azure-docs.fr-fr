---
title: Présentation du service Language Understanding (LUIS) - Azure Cognitive Services | Microsoft Docs
description: Language Understanding (LUIS) est un service API cloud qui applique une intelligence Machine Learning personnalisée au texte en langage naturel des conversations d’un utilisateur afin d’en prédire le sens général et d’en extraire des informations détaillées pertinentes. Une application cliente pour LUIS est une application de conversation qui communique avec un utilisateur en langage naturel pour accomplir une tâche. Les applications clientes comprennent notamment les applications de réseaux sociaux, les bots conversationnels et les applications de bureau à reconnaissance vocale.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: overview
ms.date: 10/06/2018
ms.author: diberry
ms.openlocfilehash: 28580a29c2ffaadfa3b3ea26cb28f103d883d576
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49637270"
---
# <a name="what-is-language-understanding-luis"></a>Qu’est-ce que le service Language Understanding (LUIS) ?

Language Understanding (LUIS) est un service API cloud qui applique une intelligence Machine Learning personnalisée au texte en langage naturel des conversations d’un utilisateur afin d’en prédire le sens général et d’en extraire des informations détaillées pertinentes. 

Une application cliente pour LUIS est une application de conversation qui communique avec un utilisateur en langage naturel pour accomplir une tâche. Les applications clientes comprennent notamment les applications de réseaux sociaux, les bots conversationnels et les applications de bureau à reconnaissance vocale.  

![Image conceptuelle de 3 applications clientes fonctionnant avec le service Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Image conceptuelle de 3 applications clientes fonctionnant avec le service Cognitive Services Language Understanding (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>Utiliser LUIS dans un bot conversationnel

<a name="Accessing-LUIS"></a>

Une fois l’application LUIS publiée, une application cliente envoie des énoncés (textes) à [l’API][endpoint-apis] du point de terminaison de traitement du langage naturel LUIS et reçoit les résultats sous forme de réponses JSON. Un bot conversationnel est un exemple d’application cliente courante pour LUIS.


![Imagerie conceptuelle du fonctionnement de LUIS avec un bot conversationnel pour prédire le texte d’un utilisateur grâce à la compréhension du langage naturel (NLP)](./media/luis-overview/luis-overview-process-2.png "Imagerie conceptuelle du fonctionnement de LUIS avec un bot conversationnel pour prédire le texte d’un utilisateur grâce à la compréhension du langage naturel (NLP)")

|Étape|Action|
|:--|:--|
|1|L’application cliente envoie _l’énoncé_ de l’utilisateur (texte dans ses propres mots), « I want to call my HR rep » (Je souhaite contacter mon représentant des ressources humaines), au point de terminaison LUIS sous la forme d’une requête HTTP.|
|2|LUIS applique le modèle formé au texte en langage naturel pour assurer une compréhension intelligente de l’entrée utilisateur. LUIS renvoie une réponse au format JSON avec l’intention principale « HRContact ». La réponse minimale du point de terminaison JSON contient l’énoncé de la requête, ainsi que l’intention présentant le score le plus élevé. Elle peut également extraire des données telles que l’entité de type de contact.|
|3|L’application cliente utilise la réponse JSON pour prendre des décisions concernant la manière dont les requêtes de l’utilisateur doivent être traitées. Ces décisions peuvent inclure un arbre de décision dans le code de l’infrastructure de bots, ainsi que des appels d’autres services. |

L’application LUIS offre l’intelligence nécessaire pour permettre à l’application cliente d’effectuer des choix pertinents. LUIS ne fournit pas ces choix. 

<!--

### Example of JSON endpoint response

The minimum JSON endpoint response contains the query utterance, and the top scoring intent. It can also extract data such as the following **Contact Type** entity. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```
-->

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Traitement en langage naturel

Une application LUIS contient un modèle de langage naturel propre à un domaine. Vous pouvez démarrer l’application LUIS avec un modèle de domaine prédéfini, créer votre propre modèle ou fusionner les éléments d’un domaine prédéfini avec vos propres informations personnalisées.

* **Modèle prédéfini** LUIS dispose de nombreux modèles de domaine prédéfinis, incluant des intentions, des énoncés et des entités prédéfinies. Vous pouvez utiliser les entités prédéfinies sans avoir à utiliser les intentions ni les énoncés du modèle prédéfini. Les [modèles de domaines prédéfinis](luis-how-to-use-prebuilt-domains.md) intègrent l’ensemble de la conception à votre intention et constituent un excellent moyen de commencer à utiliser LUIS rapidement.

* **Entités personnalisées** LUIS vous offre plusieurs moyens d’identifier vos propres intentions et entités personnalisées, y compris les entités issues du Machine Learning, les entités spécifiques ou littérales, ainsi qu’une combinaison de tous ces types d’entités.

## <a name="build-the-luis-model"></a>Générer le modèle LUIS
Générez le modèle avec les API de [création](https://aka.ms/luis-authoring-apis) ou à l’aide du portail LUIS.

Le modèle LUIS commence par les catégories d’intentions utilisateur appelées **[intentions](luis-concept-intent.md)**. Chaque intention nécessite des exemples **[d’énoncés](luis-concept-utterance.md)** utilisateur. Chaque énoncé peut fournir diverses données qui doivent être extraites avec des **[entités](luis-concept-entity-types.md)**. 

|Exemple d’énoncé d’un utilisateur|Intention|Entités|
|-----------|-----------|-----------|
|« Réserver un billet pour __Seattle__ ? »|RéserverVol|Seattle|
|« À quelle heure votre magasin __ouvre__-t-il ? »|StoreHoursAndLocation|ouvrir|
|« Planifier une réunion à __13h__ avec __Bob__ de la distribution »|ScheduleMeeting|13h, Bob|

<!--
## What is a natural language model?

A model begins with a list of general user intentions, called _intents_, such as "Book Flight" or "Contact Help Desk." You provide user's example text, called _example utterances_ for the intents. Then mark significant words or phrases in the utterance, called _entities_.


A model includes:

* **[intents](#intents)**: categories of user intentions (overall intended action or result)
* **[entities](#entities)**: specific types of data in utterances such as number, email, or name contained in text
* **[example utterances](#example-utterances)**: example text a user might enter in the client application

### Intents 

An [intent](luis-how-to-add-intents.md), short for _intention_, is a purpose or goal expressed in a user's utterance, such as booking a flight, paying a bill, or finding a news article. You create an intent for each action. A LUIS travel app may define an intent named "BookFlight." Each prediction query includes the top scored intent. 

The client application can use the top scoring intent to trigger an action. For example, when "BookFlight" intent is returned from LUIS, a client application could trigger an API call to an external service for booking a plane ticket.

### Entities

An [entity](luis-how-to-add-entities.md) represents detailed information found within the utterance that is relevant to the user's request. For example, in the utterance "Book a ticket to Paris",  a single ticket is requested, and "Paris" is a location. Two entities are found "a ticket" indicating a single ticket and "Paris" indicating the destination. 

After LUIS returns the entities found in the user’s utterance, the client application can use the list of entities as parameters to trigger an action. For example, booking a flight requires entities like the travel destination, date, and airline.
-->
<!--
### Example utterances

An example [utterance](luis-how-to-add-example-utterances.md) is text input from the user that the client application needs to understand. It may be a sentence, like "Book a ticket to Paris", or a fragment of a sentence, like "Booking" or "Paris flight." Utterances aren't always well-formed, and there can be many utterance variations for a particular intent. Add 10 to 20 example utterances to each intent and mark entities in every utterance.

|Example user utterance|Intent|Entities|
|-----------|-----------|-----------|
|"Book a flight to __Seattle__?"|BookFlight|Seattle|
|"When does your store __open__?"|StoreHoursAndLocation|open|
|"Schedule a meeting at __1pm__ with __Bob__ in Distribution"|ScheduleMeeting|1pm, Bob|
-->
## <a name="query-prediction-endpoint"></a>Interroger le point de terminaison de prédiction

Une fois que le modèle a été généré et publié sur le point de terminaison, l’application cliente envoie les énoncés à l’API du [point de terminaison](https://aka.ms/luis-endpoint-apis) de prédiction publiée. L’API applique le modèle au texte à des fins d’analyse. L’API répond par les résultats de prédiction au format JSON.  

La réponse minimale du point de terminaison JSON contient l’énoncé de la requête, ainsi que l’intention présentant le score le plus élevé. Elle peut également extraire des données telles que l’entité **Contact Type** ci-après. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```

## <a name="improve-model-prediction"></a>Améliorer la prédiction du modèle

Une fois qu’un modèle LUIS a été publié et reçoit de vrais énoncés utilisateur, LUIS offre plusieurs méthodes pour améliorer la précision de la prédiction : [l’apprentissage actif](luis-concept-review-endpoint-utterances.md) des énoncés de point de terminaison, des [listes d’expressions](luis-concept-feature.md) pour l’inclusion de mots du domaine, ainsi que des [modèles](luis-concept-patterns.md) pour réduire le nombre d’énoncés nécessaires.
<!--
### Active learning

In the [active learning](luis-how-to-review-endoint-utt.md) process, LUIS allows you to adapt the LUIS app to real-world utterances by selecting utterances it received at the endpoint for your review. You can accept or correct the endpoint prediction, retrain, and republish. LUIS learns quickly with this iterative process, taking the minimum amount of your time and effort. 

### Phrase lists 

LUIS provides [phrases lists](luis-concept-feature.md) so you can indicate important words or phrases of the model. LUIS uses these lists to add additional significance to those words and phrases that would otherwise not be found in the model.

### Patterns 

Patterns allow you to simplify an intent's utterance collection into common [templates](luis-concept-patterns.md) of word choice and word order. This allows LUIS to learn quicker by needing fewer example utterances for the intents. Patterns are a hybrid system of regular expressions and machine-learned expressions. 
-->
<a name="using-luis"></a>
<!--
## Authoring and accessing models
Author LUIS from the [authoring](https://aka.ms/luis-authoring-apis) APIs or from the LUIS portal. Query the published prediction endpoint of the model from the [endpoint](https://aka.ms/luis-endpoint-apis) APIs.
-->

## <a name="development-lifecycle"></a>Cycle de vie de développement
LUIS offre différents outils, un contrôle de version et des fonctionnalités de collaboration avec d’autres auteurs LUIS à intégrer au cycle de vie de développement complet au niveau de l’application cliente et du modèle de langage. 

## <a name="implementing-luis"></a>Implémentation de LUIS
En tant qu’API REST, LUIS est utilisable avec n’importe quel produit, service ou infrastructure exécutant une requête HTTP. La liste ci-après répertorie les principaux produits et services Microsoft utilisés avec LUIS.

La principale application cliente pour LUIS est la suivante :
* [Web App Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-3.0) crée rapidement un bot conversationnel sur lequel LUIS est activé pour discuter avec un utilisateur par le biais d’une entrée de texte. Utilise [Bot Framework][bot-framework] version [3.x](https://github.com/Microsoft/BotBuilder) ou [4.x](https://github.com/Microsoft/botbuilder-dotnet) pour bénéficier d’une expérience bot optimale.

Outils simplifiant et accélérant l’utilisation de LUIS avec un bot :
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Le package NPM offre des fonctions de création et de prédiction avec un outil en ligne de commande autonome ou sous forme d’importation. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen est un outil permettant de générer un code source C# et Typescript fortement typé à partir d’un modèle LUIS exporté.
* [Dispatch](https://aka.ms/dispatch-tool) permet d’utiliser plusieurs applications LUIS et QnA Maker à partir d’une application parente à l’aide du modèle de répartiteur.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown est un outil en ligne de commande facilitant la gestion des modèles de langage pour votre bot.

Autres services Cognitive Services utilisés avec LUIS :
* [QnA Maker][qnamaker]permet à différents types de textes de se combiner en une question et de répondre à la base de connaissances.
* [L’API Vérification orthographique Bing](../bing-spell-check/proof-text.md) propose la correction de texte avant la prédiction. 
* [Speech](../Speech-Service/overview.md) convertit les requêtes énoncées à voix haute sous forme de texte. 
* [L’Apprenant de conversation](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) accélère la création de conversations avec un bot à l’aide de LUIS.
* [Project Personality Chat](https://docs.microsoft.com/azure/cognitive-services/project-personality-chat/overview) vous permet de gérer les conversations courantes avec un bot.

## <a name="next-steps"></a>Étapes suivantes

Créez une application LUIS avec un domaine [prédéfini](luis-get-started-create-app.md) ou [personnalisé](luis-quickstart-intents-only.md). [Interrogez le point de terminaison de prédiction](luis-get-started-cs-get-intent.md) d’une application IoT publique.

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/
