---
title: Qu’est-ce que le service QnA Maker ?
description: QnA Maker est un service NLP cloud qui permet de créer facilement une couche conversationnelle naturelle sur vos données. Il peut être utilisé pour trouver la réponse la plus appropriée à une entrée donnée en langage naturel à partir de votre base de connaissances personnalisée (base d’informations).
ms.topic: overview
ms.date: 05/26/2020
ms.openlocfilehash: 5962ab2294a19c600beb00816394d96059bd4108
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873724"
---
# <a name="what-is-the-qna-maker-service"></a>Qu’est-ce que le service QnA Maker ?

[!INCLUDE [TLS 1.2 enforcement](../../../../includes/cognitive-services-tls-announcement.md)]

QnA Maker est un service cloud de traitement en langage naturel (NLP, Natural Language Processing) qui permet de créer facilement une couche conversationnelle naturelle sur vos données. Il peut être utilisé pour trouver la réponse la plus appropriée à une entrée donnée en langage naturel à partir de votre base de connaissances personnalisée (base d’informations).

Toute application de conversation qui communique avec un utilisateur en langage naturel pour répondre à une question peut être une application cliente pour QnA Maker. Les applications clientes comprennent notamment les applications de réseaux sociaux, les bots conversationnels et les applications de bureau à reconnaissance vocale.

## <a name="when-to-use-qna-maker"></a>Quand utiliser QnA Maker ?

* **Quand vous avez des informations statiques** : utilisez QnA Maker quand vous avez des informations statiques dans votre base de connaissances de réponses. Cette base de connaissances, créée avec des documents tels que [des PDF et des contenus accessibles par des URL](../concepts/content-types.md), est personnalisée en fonction de vos besoins.
* **Quand vous souhaitez fournir la même réponse à une requête, une question ou une commande** : quand différents utilisateurs soumettent la même question, la même réponse est retournée.
* **Quand vous souhaitez filtrer des informations statiques en fonction de méta-informations** : ajoutez des balises de [métadonnées](../how-to/metadata-generateanswer-usage.md) pour fournir des options de filtrage supplémentaires relatives aux utilisateurs et informations de votre application cliente. Les [échanges](../how-to/chit-chat-knowledge-base.md), types ou formats de contenu, objets de contenu et actualisations de contenu représentent des informations de métadonnées courantes.
* **Quand vous souhaitez gérer une conversation de bot incluant des informations statiques** : votre base de connaissances répond à une commande ou au texte conversationnel d’un utilisateur. Si la réponse fait partie d’un flux de conversation prédéterminé, représenté dans votre base de connaissances avec un [contexte multitour](../how-to/multiturn-conversation.md), le bot peut facilement fournir ce flux.

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>Utiliser une base de connaissances QnA Maker dans un chatbot

Une fois qu’une base de connaissances QnA Maker est publiée, une application cliente envoie une question au point de terminaison de votre base de connaissances et reçoit les résultats sous forme de réponse JSON. Un chatbot est un exemple d’application cliente courante pour QnA Maker.

![Poser une question à un bot et obtenir une réponse à partir du contenu de la base de connaissances](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Étape|Action|
|:--|:--|
|1|L’application cliente envoie la _question_ de l’utilisateur (texte dans ses propres mots) « How do I programmatically update my Knowledge Base? » au point de terminaison de votre base de connaissances.|
|2|QnA Maker utilise la base de connaissances entraînée pour fournir la réponse correcte et les invites de suivi qui peuvent être utilisées pour affiner la recherche de la meilleure réponse. QnA Maker retourne une réponse au format JSON.|
|3|L’application cliente utilise la réponse JSON pour prendre des décisions concernant la manière de poursuivre la conversation. Ces décisions peuvent inclure l’affichage de la réponse principale et la présentation de choix supplémentaires pour affiner la recherche de la meilleure réponse. |
|||

## <a name="what-is-a-knowledge-base"></a>Qu’est-ce qu’une base de connaissances ?

QnA Maker [importe votre contenu](../concepts/knowledge-base.md) dans une base de connaissances comprenant des ensembles de questions et de réponses. Le processus d’importation extrait des informations sur la relation entre les différentes parties de votre contenu structuré et semi-structuré pour définir des relations entre les ensembles de questions et de réponses. Vous pouvez modifier ces ensembles de questions et de réponses ou en ajouter de nouveaux.

Le contenu de l’ensemble question-réponse comprend les éléments suivants :
* Toutes les autres formes de la question
* Les étiquettes de métadonnées utilisées pour filtrer les choix de réponse lors de la recherche
* Des invites de suivi pour poursuivre le perfectionnement de la recherche

![Exemple de question et de réponse avec des métadonnées](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Une fois que vous avez publié votre base de connaissances, une application cliente envoie la question d’un utilisateur au point de terminaison. Votre service QnA Maker traite la question et y répond avec la meilleure réponse.

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Créer un bot, le gérer et publier sur le bot sans code

Le portail QnA Maker offre tout l’environnement nécessaire pour créer une base de connaissances. Vous pouvez importer des documents sous leur forme actuelle dans votre base de connaissances. Ces documents (tels qu’un FAQ, un manuel produit, une feuille de calcul ou une page web) sont convertis en ensembles de questions et de réponses. Chaque ensemble est analysé pour identifier des invites de suivi et est connecté à d’autres ensembles. Le format _Markdown_ final prend en charge les présentations riches, notamment les images et les liens.

Une fois votre base de connaissances modifiée, publiez-la sur un [bot Azure Web App](https://azure.microsoft.com/services/bot-service/) de travail sans écrire le moindre code. Testez votre bot dans le [portail Azure](https://portal.azure.com) ou procédez au téléchargement et poursuivez le développement.

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>La qualité de la recherche et le classement permettent de fournir la meilleure réponse possible

Le système de QnA Maker s’appuie sur une approche de classement par couches. Les données sont stockées dans la Recherche Azure, qui sert également de première couche de classement. Les meilleurs résultats de la Recherche Azure sont ensuite transmis par le biais du modèle de reclassement NLP de QnA Maker pour produire les résultats finaux et le score de confiance.

## <a name="qna-maker-improves-the-conversation-process"></a>QnA Maker améliore le processus de conversation

QnA Maker fournit des invites multitours et applique un apprentissage actif pour vous aider à améliorer vos ensembles de questions et réponses de base.

Les **invites multitours** vous donnent la possibilité d’associer les paires de questions et réponses. Cette association permet à l’application cliente de fournir une réponse principale et fournit davantage de questions pour affiner la recherche d’une réponse finale.

Dès lors que la base de connaissances reçoit des questions des utilisateurs au point de terminaison publié, QnA Maker applique l’**apprentissage actif** à ces questions réelles pour suggérer des modifications à apporter à la base de connaissances afin d’en améliorer la qualité.

## <a name="development-lifecycle"></a>Cycle de vie de développement

QnA Maker offre des fonctionnalités de création, d’entraînement et de publication ainsi que des autorisations de collaboration, s’intégrant à l’ensemble du cycle de vie de développement.

> [!div class="mx-imgBorder"]
> ![Image conceptuelle du cycle de développement](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="how-do-i-start"></a>Par où commencer ?

**Étape 1** : Créez une ressource QnA Maker dans le [portail Azure](https://portal.azure.com).

**Étape 2** : Créez une base de connaissances dans le portail [QnA Maker](https://www.qnamaker.ai). Ajoutez des [fichiers et des URL](../concepts/content-types.md) pour créer la base de connaissances.

**Étape 3** : Publiez votre base de connaissances et testez-la à partir de votre point de terminaison personnalisé à l’aide de [cURL ou de Postman](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md).

**Étape 4** : À partir de votre application cliente, appelez programmatiquement le point de terminaison de votre base de connaissances. L’application cliente traite la réponse JSON afin de montrer la meilleure réponse à l’utilisateur.

## <a name="next-steps"></a>Étapes suivantes
QnA Maker fournit tout ce dont vous avez besoin pour créer, gérer et déployer une base de connaissances personnalisée.

> [!div class="nextstepaction"]
> [Passer en revue les derniers changements](../whats-new.md)
