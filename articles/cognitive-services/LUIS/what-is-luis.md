---
title: Qu’est-ce que le service Language Understanding (LUIS) ?
description: Language Understanding (LUIS) est un service API cloud qui applique une intelligence Machine Learning personnalisée au texte en langage naturel des conversations d’un utilisateur afin d’en prédire le sens général et d’en extraire des informations détaillées pertinentes.
ms.topic: overview
ms.date: 05/05/2020
ms.openlocfilehash: d09aaabeec1d22132843ba98472e1cd89ba95815
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592812"
---
# <a name="what-is-language-understanding-luis"></a>Qu’est-ce que le service Language Understanding (LUIS) ?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) est un service API cloud qui applique une intelligence Machine Learning personnalisée au texte en langage naturel des conversations d’un utilisateur afin d’en prédire le sens général et d’en extraire des informations détaillées pertinentes.

Une application cliente pour LUIS est une application de conversation qui communique avec un utilisateur en langage naturel pour accomplir une tâche. Les applications clientes comprennent notamment les applications de réseaux sociaux, les bots conversationnels et les applications de bureau à reconnaissance vocale.

![Image conceptuelle de 3 applications clientes fonctionnant avec Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Image conceptuelle de 3 applications clientes fonctionnant avec Cognitive Services Language Understanding (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>Utiliser LUIS dans un bot conversationnel

<a name="Accessing-LUIS"></a>

Une fois l’application LUIS publiée, une application cliente envoie des énoncés (textes) à [l’API][endpoint-apis] du point de terminaison de traitement du langage naturel LUIS et reçoit les résultats sous forme de réponses JSON. Un bot conversationnel est un exemple d’application cliente courante pour LUIS.


![Image conceptuelle de LUIS fonctionnant avec un bot conversationnel pour prédire le texte de l’utilisateur avec une compréhension du langage naturel (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Image conceptuelle de LUIS fonctionnant avec un bot conversationnel pour prédire le texte de l’utilisateur avec une compréhension du langage naturel (NLP)")

|Étape|Action|
|:--|:--|
|1|L’application cliente envoie _l’énoncé_ de l’utilisateur (texte dans ses propres mots), « I want to call my HR rep » (Je souhaite contacter mon représentant des ressources humaines), au point de terminaison LUIS sous la forme d’une requête HTTP.|
|2|LUIS vous permet de concevoir vos modèles de langage personnalisés pour ajouter de l’intelligence à votre application. Les modèles de langage formés par apprentissage automatique prennent le texte d’entrée non structuré de l’utilisateur et retournent une réponse au format JSON, avec une intention principale, `HRContact`. La réponse minimale du point de terminaison JSON contient l’énoncé de la requête, ainsi que l’intention présentant le score le plus élevé. Elle peut également extraire des données telles que l’entité _Type de contact_.|
|3|L’application cliente utilise la réponse JSON pour prendre des décisions concernant la manière dont les requêtes de l’utilisateur doivent être traitées. Ces décisions peuvent inclure un arbre de décision dans le code de l’infrastructure de bots, ainsi que des appels d’autres services. |

L’application LUIS offre l’intelligence nécessaire pour permettre à l’application cliente d’effectuer des choix pertinents. LUIS ne fournit pas ces choix.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Traitement en langage naturel

Votre application LUIS contient un modèle de langage naturel propre à un domaine. Vous pouvez démarrer l’application LUIS avec un modèle de domaine prédéfini, créer votre propre modèle ou fusionner les éléments d’un domaine prédéfini avec vos propres informations personnalisées.

* **Modèle prédéfini** LUIS dispose de nombreux modèles de domaine prédéfinis, incluant des intentions, des énoncés et des entités prédéfinies. Vous pouvez utiliser les entités prédéfinies sans avoir à utiliser les intentions ni les énoncés du modèle prédéfini. Les [modèles de domaines prédéfinis](luis-how-to-use-prebuilt-domains.md) intègrent l’ensemble de la conception à votre intention et constituent un excellent moyen de commencer à utiliser LUIS rapidement.

* Le **modèle personnalisé** LUIS vous offre plusieurs façons d’identifier vos propres modèles personnalisés, notamment les intentions et les entités. Les entités incluent des entités apprises automatiquement, des entités spécifiques ou littérales, et une combinaison des deux.

## <a name="build-the-luis-model"></a>Générer le modèle LUIS
Générez le modèle avec les API de [création](https://go.microsoft.com/fwlink/?linkid=2092087) ou à l’aide du [portail LUIS](https://www.luis.ai).

Le modèle LUIS commence par les catégories d’intentions utilisateur appelées **[intentions](luis-concept-intent.md)** . Chaque intention nécessite des exemples **[d’énoncés](luis-concept-utterance.md)** utilisateur. Chaque énoncé peut fournir des données qui doivent être extraites.

|Exemple d’énoncé d’un utilisateur|Intentionnel|Données extraites|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|RéserverVol|Seattle|
|`When does your store open?`|StoreHoursAndLocation|ouvert|
|`Schedule a meeting at 1pm with Bob in Distribution`|ScheduleMeeting|13h, Bob|

## <a name="query-prediction-endpoint"></a>Interroger le point de terminaison de prédiction

Une fois votre application entraînée et publiée sur le point de terminaison, l’application cliente envoie les énoncés à l’API du [point de terminaison](https://go.microsoft.com/fwlink/?linkid=2092356) de prédiction. L’API applique l’application à l’énoncé d’analyse et répond avec les résultats de prédiction dans un format JSON.

La réponse minimale du point de terminaison JSON contient l’énoncé de la requête, ainsi que l’intention présentant le score le plus élevé. Elle peut également extraire des données telles que l’entité **Contact Type** et ke sentiment général ci-après.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "normalizedQuery": "i want to call my hr rep",
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "negative",
            "score": 0.103343368
        }
    }
}
```

## <a name="improve-model-prediction"></a>Améliorer la prédiction du modèle

Une fois que votre application LUIS est publiée et reçoit des énoncés utilisateur réels, LUIS fournit un [apprentissage actif](luis-concept-review-endpoint-utterances.md) des énoncés de point de terminaison pour améliorer la précision de la prédiction.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Cycle de vie de développement
LUIS offre différents outils, un contrôle de version et des fonctionnalités de collaboration avec d’autres auteurs LUIS à intégrer au [cycle de vie de développement complet](luis-concept-app-iteration.md).

## <a name="implementing-luis"></a>Implémentation de LUIS
En tant qu’API REST, Language Understanding (LUIS) est utilisable avec n’importe quel produit, service ou infrastructure avec une requête HTTP. La liste ci-après répertorie les principaux produits et services Microsoft utilisés avec LUIS.

La principale application cliente pour LUIS est la suivante :
* [Web App Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) crée rapidement un bot conversationnel sur lequel LUIS est activé pour discuter avec un utilisateur par le biais d’une entrée de texte. Utilise [Bot Framework][bot-framework] version [4.x](https://github.com/Microsoft/botbuilder-dotnet) pour bénéficier d’une expérience bot optimale.

Outils simplifiant et accélérant l’utilisation de LUIS avec un bot :
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Le package NPM offre des fonctions de création et de prédiction avec un outil en ligne de commande autonome ou sous forme d’importation.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen est un outil permettant de générer un code source C# et Typescript fortement typé à partir d’un modèle LUIS exporté.
* [Dispatch](https://aka.ms/dispatch-tool) permet d’utiliser plusieurs applications LUIS et QnA Maker à partir d’une application parente à l’aide du modèle de répartiteur.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown est un outil en ligne de commande facilitant la gestion des modèles de langage pour votre bot.
* [Bot Framework - Composer](https://github.com/microsoft/BotFramework-Composer) - Outil de développement intégré, destiné aux développeurs et aux équipes pluridisciplinaires pour créer des bots et des expériences de conversation avec Microsoft Bot Framework

Autres services Cognitive Services utilisés avec LUIS :
* [QnA Maker][qnamaker] permet à différents types de textes de se combiner en une question et de répondre à la base de connaissances.
* [Speech](../Speech-Service/overview.md) convertit les requêtes énoncées à voix haute sous forme de texte.
* [L’Apprenant de conversation](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) accélère la création de conversations avec un bot à l’aide de LUIS.

Exemples utilisant LUIS :
* Dépôt GitHub [Conversational AI](https://github.com/Microsoft/AI).
* [Bot Framework - Exemples de bot](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Étapes suivantes

* [Nouveautés](whats-new.md)
* Créez une application LUIS avec un domaine [prédéfini](luis-get-started-create-app.md) ou [personnalisé](luis-quickstart-intents-only.md).
* [Interrogez le point de terminaison de prédiction](luis-get-started-get-intent-from-browser.md) d’une application IoT publique.
* [Ressources pour les développeurs](developer-reference-resource.md) pour LUIS.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
