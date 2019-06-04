---
title: Bonnes pratiques - QnA Maker
titlesuffix: Azure Cognitive Services
description: Suivez ces bonnes pratiques pour améliorer votre base de connaissances et fournir des résultats plus pertinents aux utilisateurs finaux de votre application/bot conversationnel.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/10/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: b599beb6a8d14d0e62d236251fb5f5b1e1a8bcfd
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496938"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Bonnes pratiques pour une base de connaissances QnA Maker
Le [cycle de vie du développement d’une base de connaissances](../Concepts/development-lifecycle-knowledge-base.md) vous guide de bout en bout sur la façon de gérer votre base de connaissances. Suivez ces bonnes pratiques pour améliorer votre base de connaissances et fournir des résultats plus pertinents aux utilisateurs finaux de votre application/bot conversationnel.

## <a name="extraction"></a>Extraction
Le service QnA Maker améliore en permanence les algorithmes qui extraient des entités QnA à partir du contenu, et étend la liste des formats HTML et des formats de fichiers pris en charge. Suivez les [instructions](../Concepts/data-sources-supported.md) d’extraction de données en fonction de votre type de document. 

En général, les pages FAQ doivent être autonomes et ne pas être combinées avec d’autres informations. Les manuels de produits doivent avoir des titres clairs et, si possible, une page d’index. 

## <a name="creating-good-questions-and-answers"></a>Créer de bonnes questions et réponses

### <a name="good-questions"></a>Bonnes questions

Les meilleures questions sont simples. Identifiez le mot ou l’expression clé de chaque question, puis créez une question simple pour ce mot ou cette expression. 

Ajoutez autant de questions alternatives que vous le souhaitez, en restant toutefois sur des modifications simples. Le fait d’ajouter des mots ou des formulations qui ne font pas partie de l’objectif principal de la question n’aide pas QnA Maker à trouver une correspondance. 

### <a name="good-answers"></a>Bonnes réponses

Les meilleures réponses sont simples, mais pas trop (par exemple, oui et non). Si votre réponse doit comporter des liens vers d’autres sources ou offrir une expérience enrichie avec des contenus multimédias et des liens, utilisez le [balisage](../how-to/metadata-generateanswer-usage.md) pour distinguer le type de réponse attendu, puis envoyez cette balise avec la requête pour obtenir la bonne version de la réponse.

## <a name="chit-chat"></a>Échanges de conversation (chit-chat)
Ajoutez facilement des échanges à votre bot afin de rendre ses conversations plus naturelles et engageantes. Vous pouvez facilement ajouter des jeux de données de conversation instantanée chit à partir de personnalités prédéfinies lors de la création de votre base de connaissances et les modifier à tout moment. Découvrez comment [ajouter des échanges de conversation à votre base de connaissances](../How-To/chit-chat-knowledge-base.md). 

### <a name="choosing-a-personality"></a>Choix d’une personnalité
Conversation en Chit est pris en charge pour plusieurs personnalités prédéfinies : 

|Personnalité |Fichier de jeu de données QnA Maker |
|---------|-----|
|Professionnel |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Convivial |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Beau |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Soins |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Enthousiaste |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Les réponses vont du formel à l’informel, en passant par l’irrévérencieux. Vous devez sélectionner la personnalité qui correspond le plus au ton souhaité pour votre bot. Vous pouvez afficher le [jeux de données](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)et choisissez une qui sert de base pour votre bot, puis personnaliser les réponses. 

### <a name="edit-bot-specific-questions"></a>Modifier les questions propres au bot
Il existe certaines questions propres au bot qui font partie du jeu de données d’échanges de conversation et qui ont été remplies avec des réponses génériques. Modifiez ces réponses pour qu’elles reflètent mieux les caractéristiques de votre bot. 

Nous vous recommandons de rendre les questions-réponses d’échanges de conversation suivantes plus spécifiques :

* Qui êtes-vous ?
* Que pouvez-vous faire ?
* Quel âge avez-vous ?
* Qui vous a créé ?
* Hello
   

## <a name="rankingscoring"></a>Classement/scoring
Essayez d’optimiser l’utilisation des fonctionnalités de classement que QnA Maker prend en charge. Ces fonctionnalités augmentent la probabilité de retourner une réponse pertinente à une requête utilisateur.

### <a name="choosing-a-threshold"></a>Choix d’un seuil
Le score de confiance par défaut utilisé comme seuil est de 50, mais vous pouvez le changer pour votre base de connaissances en fonction de vos besoins. Chaque base de connaissances étant différente, vous devez procéder à des test et choisir le seuil qui convient le mieux à votre base de connaissances. Pour en savoir plus sur le score de confiance, consultez [cet article](../Concepts/confidence-score.md). 

### <a name="choosing-ranker-type"></a>Choix du type thématiques
Par défaut, QnA Maker effectue une recherche dans les questions et réponses. Si vous souhaitez effectuer une recherche dans les questions uniquement, pour générer une réponse, utilisez le `RankerType=QuestionOnly` dans le corps POST de la demande GenerateAnswer.

### <a name="add-alternate-questions"></a>Ajouter des questions alternatives
Les [questions alternatives](../How-To/edit-knowledge-base.md) augmentent la probabilité de trouver une correspondance avec une requête utilisateur. Les questions alternatives sont utiles quand il existe plusieurs manières de poser une question. C’est notamment le cas pour les variantes syntaxiques et lexicales.

|Requête d’origine|Autres requêtes|Modifier| 
|--|--|--|
|Un stationnement est-il disponible ?|Vous avez un parking ?|Variante syntaxique|
 |Salut|Bonjour<br>Ça va ?|Variante lexicale ou argot|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Utiliser des balises de métadonnées pour filtrer les questions et les réponses

Les [métadonnées](../How-To/edit-knowledge-base.md) vous permettent aussi de réduire le nombre des résultats d’une requête utilisateur grâce aux balises de métadonnées. La réponse de la base de connaissances peut varier en fonction du mot clé de métadonnées, même si la requête est la même. Par exemple, dans le cas d’une chaîne de restaurants, la question *« Où se trouve le parking ? »* peut donner une réponse diffère en fonction du lieu du restaurant (ici, les métadonnées sont *Location: Seattle* versus *Location: Redmond*).

### <a name="use-synonyms"></a>Utiliser des synonymes
Les synonymes en langue anglaise sont pris en charge. Utilisez des [variantes de mots](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) (sans respect de la casse) afin d'ajouter des synonymes à des mots clés qui prennent différentes formes. Les synonymes doivent être ajoutés au niveau du service QnA Maker et être partagés par toutes les bases de connaissances du service.

|Mot d’origine|Synonymes|
|--|--|
|buy|purchase<br>netbanking<br>net banking|

### <a name="use-distinct-words-to-differentiate-questions"></a>Utiliser des mots distincts pour différencier les questions
L’algorithme de classement de QnA Maker, qui recherche une correspondance entre une requête utilisateur et les questions de la base de connaissances, retourne de meilleurs résultats si chaque question répond à un besoin différent. La répétition du même mot défini entre les questions réduit la probabilité de sélection de la réponse appropriée pour une requête utilisateur qui contient ces mots. 

Par exemple, vous pouvez avoir deux entités QnA distinctes avec les questions suivantes :

|Entités QnA|
|--|
|où est l’*emplacement* de stationnement|
|où est *l’emplacement* du distributeur automatique de billets|

Ces deux entités QnA employant des mots très similaires, elles pourraient générer des scores très similaires pour de nombreuses requêtes d’utilisateur utilisant la tournure de phrase *« où est l’emplacement de `<x>` ».* Essayez plutôt de les distinguer clairement avec des requêtes du type *« où est le parking »* et *« où est le distributeur automatique de billets »* , en évitant des mots comme « emplacement » susceptibles de figurer dans de nombreuses questions de la base de connaissances. 

## <a name="collaborate"></a>Travailler en collaboration
QnA Maker permet aux utilisateurs de [travailler en collaboration](../How-to/collaborate-knowledge-base.md) sur une base de connaissances. Les utilisateurs doivent avoir accès au groupe de ressources Azure QnA Maker pour accéder à la base de connaissances. Certaines organisations souhaitent externaliser la mise à jour et la gestion des bases de connaissances tout en pouvant quand même protéger l’accès à leurs ressources Azure. Ce modèle éditeur-approbateur s’obtient en configurant deux [services QnA Maker](../How-to/set-up-qnamaker-service-azure.md) identiques dans des abonnements différents et en sélectionnant l’un d’entre eux pour le cycle de modification-test. À la fin des tests, le contenu de la base de connaissances est transféré, par un processus [importation-exportation](../Tutorials/migrate-knowledge-base.md), au service QnA Maker de l’approbateur, qui publiera la base de connaissances et mettra à jour le point de terminaison.

## <a name="active-learning"></a>Apprentissage actif

L’[apprentissage actif](../How-to/improve-knowledge-base.md) est le plus à même de suggérer d’autres questions quand il dispose d’un large éventail qualitatif et quantitatif de requêtes utilisateur. Il est important d’autoriser les requêtes utilisateur des applications clientes à participer à la boucle de commentaires sur l’apprentissage actif sans censure. Une fois que les questions sont suggérées dans le portail QnA Maker, vous pouvez **[filtrer par suggestions](../How-To/improve-knowledge-base.md#add-active-learning-suggestion-to-knowledge-base)** puis passez en revue et accepter ou rejeter ces suggestions. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Modifier une base de connaissances](../How-to/edit-knowledge-base.md)
