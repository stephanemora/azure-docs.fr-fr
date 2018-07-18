---
title: À propos de Language Understanding (LUIS) dans Azure | Microsoft Docs
description: Apprenez à utiliser Language Understanding (LUIS) pour transférer la puissance d’apprentissage automatique vers vos applications.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/22/2017
ms.author: v-geberr
ms.openlocfilehash: 1c68c586fb799a540f70804d181aa66b2bda9e97
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952536"
---
# <a name="what-is-language-understanding-luis"></a>Qu’est-ce que le service Language Understanding (LUIS) ?
Language Understanding (LUIS) est un service cloud qui applique l’apprentissage automatique personnalisé aux conversations d’utilisateur, aux textes en langage naturel pour prédire le sens général et extraire des informations détaillées et pertinentes. 

Une application cliente pour LUIS peut être une application de conversation qui communique avec un utilisateur dans un langage naturel pour effectuer une tâche. Les exemples d’applications client incluent des applications de réseaux sociaux, des chatbots, et des applications vocales de bureau.  

![Image conceptuelle de 3 applications envoyant des informations à LUIS](./media/luis-overview/luis-entry-point.png)

## <a name="what-is-a-luis-app"></a>Qu’est-ce que l’application LUIS ?
Une application LUIS contient un modèle de langage naturel spécifique à un domaine, que vous concevez. Vous pouvez démarrer votre application LUIS avec un modèle de domaine prédéfini, créer votre propre modèle ou fusionner les éléments d’un domaine prédéfini avec vos propres informations personnalisées.

[Les modèles de domaine prédéfinis](luis-how-to-use-prebuilt-domains.md) incluent tous ces éléments et constituent un excellent moyen pour commencer à utiliser LUIS rapidement.

L’application LUIS contient également des paramètres d’intégration, des [collaborateurs](luis-concept-collaborator.md), et des [versions](luis-concept-version.md).

## <a name="using-a-luis-app"></a>Utiliser une application LUIS
<a name="Accessing-LUIS"></a> Une fois votre application LUIS publiée, votre application cliente envoie des énoncés à l’[API de point de terminaison][endpoint-apis] LUIS et reçoit les résultats de prédiction en tant que réponses JSON.

Dans le diagramme suivant, votre chatbot client envoie d’abord un texte sur ce qu’une personne souhaite avec ses propres mots à LUIS dans une requête HTTP. Ensuite, LUIS applique votre modèle formé au langage naturel pour comprendre l’entrée utilisateur et retourne une réponse au format JSON (JavaScript Object Notation). Troisièmement, votre chatbot client utilise la réponse JSON pour répondre aux demandes de l’utilisateur. 

![Images conceptuelles de LUIS fonctionnant avec Chatbot](./media/luis-overview/luis-overview-process-2.png)

### <a name="example-of-json-endpoint-response"></a>Exemple de réponse de point de terminaison JSON

La réponse de point de terminaison JSON contient au moins l’énoncé de requête et l’intention à notation supérieure. 

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

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>
## <a name="what-is-a-natural-language-model"></a>Qu’est-ce qu’un modèle de langage naturel ?
Un modèle commence par une liste d’intentions générales d’utilisateur, appelées _intentions_, telles que « Réserver un vol » ou « Contacter le support technique ». Vous fournissez des exemples de texte utilisateur, appelés _exemples d’énoncés_ pour les intentions. Puis marquez les expressions ou mots importants dans l’énoncé, appelés _entités_.


Un modèle inclut :

* **[intensions](#intents)** : catégories d’intentions utilisateur (action ou résultat prévu)
* **[entités](#entities)** : types de données spécifiques dans les énoncés, tels que le nombre, l’e-mail, ou le nom
* **[exemples d’énoncés](#example-utterances)** : exemples de texte qu’un utilisateur peut entrer dans l’application cliente

### <a name="intents"></a>Intentions 
Une [intention](luis-how-to-add-intents.md)est un but ou un objectif exprimé dans l’énoncé d’un utilisateur, tel que réserver un vol, régler une facture ou rechercher un article de presse. Vous créez une intention pour chaque action. Une application de voyage LUIS peut définir une intention nommée « BookFlight ». Votre application cliente peut utiliser l’intention à notation supérieure pour déclencher une action. Par exemple, lorsque l’intention « BookFlight » est retournée par LUIS, votre application cliente peut déclencher un appel d’API vers un service externe pour réserver un billet d’avion.

### <a name="entities"></a>Entités
Une [entité](luis-how-to-add-entities.md) représente des informations détaillées qui figurent dans l’énoncé correspondant à la requête de l’utilisateur. Par exemple, dans l’énoncé « Réserver un billet pour Paris », un seul billet est demandé, et « Paris » est un lieu. Deux entités sont détectées : « un billet », qui indique qu’un seul billet est demandé, et « Paris », qui indique la destination. 

Une fois que LUIS a retourné les entités trouvées dans l’énoncé de l’utilisateur, l’application cliente peut utiliser la liste des entités en tant que paramètres pour déclencher une action. Par exemple, la réservation d’un vol nécessite des entités telles que la destination du voyage, les dates et la compagnie aérienne.

LUIS propose plusieurs moyens d’identifier et de classer les entités.

* **Entités prédéfinies** LUIS possède de nombreux modèles de domaine prédéfinis, y compris les intentions, les énoncés, et les [entités prédéfinies](luis-prebuilt-entities.md). Vous pouvez utiliser les entités prédéfinies sans avoir à utiliser les intentions ni les énoncés du modèle prédéfini. Les entités prédéfinies vous font gagner du temps.

* **Entités personnalisées** LUIS propose plusieurs moyens d’identifier vos propres [entités](luis-concept-entity-types.md) personnalisées, y compris les entités issues de l’apprentissage automatique, les entités spécifiques ou littérales, et une combinaison d’entités littérales et issues de l’apprentissage automatique.

### <a name="example-utterances"></a>Exemples d’énoncés
Un exemple d’[énoncé](luis-how-to-add-example-utterances.md) est une entrée de texte de l’utilisateur que l’application cliente doit comprendre. Il peut s’agir d’une phrase, telle que « Réserver un billet pour Paris », ou d’un fragment de phrase, comme « Réservation » ou « Vol pour Paris ». Les énoncés ne sont pas toujours bien formés, et il peut y avoir de nombreuses variations d’énoncé pour une intention particulière. Ajoutez 10 à 20 exemples d’énoncés pour chaque intention et marquez les entités dans chaque énoncé.

|Exemple d’énoncé d’un utilisateur|Intention|Entités|
|-----------|-----------|-----------|
|« Réserver un billet pour __Seattle__ ? »|BookFlight|Seattle|
|« À quelle heure votre magasin __ouvre__-t-il ? »|StoreHoursAndLocation|ouvrir|
|« Planifier une réunion à __13h__ avec __Bob__ de la distribution »|ScheduleMeeting|13h, Bob|

## <a name="improve-prediction-accuracy"></a>Améliorer la précision de la prédiction
Une fois que votre application LUIS est publiée et reçoit de vrais énoncés d’utilisateur, LUIS propose plusieurs méthodes pour améliorer la précision de la prédiction : l’[apprentissage actif](#active-learning) des énoncés de point de terminaison, des [listes d’expression](#phrase-lists) pour l’inclusion de domaines de mots, et des [modèles](#patterns) pour réduire le nombre d’énoncés nécessaires.

### <a name="active-learning"></a>Apprentissage actif
Dans le processus d’[apprentissage actif](luis-how-to-review-endoint-utt.md), LUIS vous permet d’adapter votre application aux énoncés réels en sélectionnant les énoncés reçus au point de terminaison pour votre révision. Vous pouvez accepter ou corriger la prédiction du point de terminaison, affiner et republier. LUIS apprend plus rapidement grâce à ce processus itératif et nécessite moins de temps et d’efforts. 

### <a name="phrase-lists"></a>Listes d’expressions 
LUIS fournit des [listes d’expression](luis-concept-feature.md) pour que vous puissiez indiquer les expressions ou mots importants à votre modèle de domaine. LUIS utilise ces listes pour renforcer l’importance de ces mots et expressions qui normalement ne se trouvent pas dans le modèle.

### <a name="patterns"></a>Modèles 
Ces modèles vous permettent de simplifier la collection d’énoncés d’intention en [modèles](luis-concept-patterns.md) courants de choix des mots et d’ordre des mots. Cela permet à LUIS d’apprendre plus rapidement, car il nécessite moins d’exemples d’énoncés pour les intentions. Les modèles sont un système hybride d’expressions régulières et d’expressions issues de l’apprentissage automatique. 

<a name="using-luis"></a>

## <a name="authoring-and-accessing-luis"></a>Création et accès à LUIS
Générez votre application LUIS depuis le site web LUIS ou par programmation avec les API de [création](https://aka.ms/luis-authoring-apis), ou en utilisant les deux manières en fonction des besoins de création. Accédez à votre application LUIS publiée par le [point de terminaison](https://aka.ms/luis-endpoint-apis) de la requête. 

LUIS possède trois sites web dans le monde entier, selon votre région de création. La région de création détermine la région Azure dans laquelle vous pouvez publier votre application LUIS.
<!--
|Authoring region|Publishing region(s)|
|--|--|
|[www.luis.ai](https://www.luis.ai)|**U.S.**<br>West US<br>West US 2<br>East US<br>East US 2<br>South Central US<br>West Central US<br><br>**Asia**<br>Southeast Asia<br>East Asia<br><br>**South America**<br>Brazil South |
|[au.luis.ai](https://au.luis.ai)|Australia East|
|[eu.luis.ai](https://eu.luis.ai)|West Europe<br>North Europe|
-->

En savoir [plus](luis-reference-regions.md) sur la création et la publication de régions.

## <a name="what-technologies-work-with-luis"></a>Quelles technologies fonctionnent avec LUIS ?
Plusieurs technologies Microsoft fonctionnent avec LUIS :

* [L’API Vérification orthographique Bing](../bing-spell-check/proof-text.md) propose la correction de texte avant la prédiction. 
* [Bot Framework][bot-framework] permet à un chatbot de discuter avec un utilisateur par le biais d’une entrée de texte. Sélectionnez un kit de développement logiciel (SDK) [3.x](https://github.com/Microsoft/BotBuilder) ou [4.x](https://github.com/Microsoft/botbuilder-dotnet) pour une expérience bot optimale.
* [QnA Maker][qnamaker]permet à différents types de textes de se combiner en une question et de répondre à la base de connaissances.
* [Speech](../Speech/home.md) convertit les requêtes de langue parlée en texte. Une fois converties en texte, les requêtes sont traitées par LUIS. Consultez le [Kit de développement logiciel (SDK) de reconnaissance vocale](https://aka.ms/csspeech) pour plus d’informations.
* [L’Analyse de texte](../text-analytics/overview.md) fournit une analyse des sentiments et l’extraction de données de phrases clés.

## <a name="next-steps"></a>Étapes suivantes
Créez une application LUIS avec un domaine [prédéfini](luis-get-started-create-app.md) ou [personnalisé](luis-quickstart-intents-only.md).

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/