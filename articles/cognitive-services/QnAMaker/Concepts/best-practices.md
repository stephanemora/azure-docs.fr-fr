---
title: Bonnes pratiques - QnA Maker
titlesuffix: Azure Cognitive Services
description: Suivez ces bonnes pratiques pour améliorer votre base de connaissances et fournir des résultats plus pertinents aux utilisateurs finaux de votre application/bot conversationnel.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/24/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 63025a299f6b7b48936be9702522a832a1d77330
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074796"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Bonnes pratiques pour une base de connaissances QnA Maker
Le [cycle de vie du développement d’une base de connaissances](../Concepts/development-lifecycle-knowledge-base.md) vous guide de bout en bout sur la façon de gérer votre base de connaissances. Suivez ces bonnes pratiques pour améliorer votre base de connaissances et fournir des résultats plus pertinents aux utilisateurs finaux de votre application/bot conversationnel.

## <a name="extraction"></a>Extraction
Le service QnA Maker améliore en permanence les algorithmes qui extraient des entités QnA à partir du contenu, et étend la liste des formats HTML et des formats de fichiers pris en charge. Suivez les [instructions](../Concepts/data-sources-supported.md) d’extraction de données en fonction de votre type de document. 

En général, les pages FAQ doivent être autonomes et ne pas être combinées avec d’autres informations. Les manuels de produits doivent avoir des titres clairs et, si possible, une page d’index. 

## <a name="chit-chat"></a>Échanges de conversation (chit-chat)
Ajoutez facilement des échanges à votre bot afin de rendre ses conversations plus naturelles et engageantes. Vous pouvez aisément ajouter des jeux de données d’échanges de conversation pour trois personnalités prédéfinies lors de la création de votre base de connaissances, et les modifier à tout moment. Découvrez comment [ajouter des échanges de conversation à votre base de connaissances](../How-To/chit-chat-knowledge-base.md). 

### <a name="choosing-a-personality"></a>Choix d’une personnalité
Les échanges de conversation sont pris en charge pour trois personnalités prédéfinies : 

|Personnalités|
|--|
|Le professionnel|
|L’ami|
|Le comique|

Les réponses vont du formel à l’informel, en passant par l’irrévérencieux. Vous devez sélectionner la personnalité qui correspond le plus au ton souhaité pour votre bot. Vous pouvez afficher les jeux de données, en choisir un qui sert de base pour votre bot, puis personnaliser les réponses. 

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


### <a name="add-alternate-questions"></a>Ajouter des questions alternatives
Les [questions alternatives](../How-To/edit-knowledge-base.md) augmentent la probabilité de trouver une correspondance avec une requête utilisateur. Les questions alternatives sont utiles quand il existe plusieurs manières de poser une question. C’est notamment le cas pour les variantes syntaxiques et lexicales.

|Requête d’origine|Autres requêtes|Modifier| 
|--|--|--|
|Un stationnement est-il disponible ?|Vous avez un parking ?|Variante syntaxique|
 |Salut|Bonjour<br>Ça va ?|Variante lexicale ou argot|

### <a name="use-metadata-filters"></a>Utiliser des filtres de métadonnées
Les [métadonnées](../How-To/edit-knowledge-base.md) vous permettent aussi de réduire le nombre des résultats d’une requête utilisateur en les filtrant. La réponse de la base de connaissances peut varier en fonction du mot clé de métadonnées, même si la requête est la même. Par exemple, dans le cas d’une chaîne de restaurants, la question *« Où se trouve le parking ? »* peut donner une réponse diffère en fonction du lieu du restaurant (ici, les métadonnées sont *Location: Seattle* versus *Location: Redmond*).

### <a name="use-synonyms"></a>Utiliser des synonymes
Les synonymes en langue anglaise sont pris en charge. Utilisez des [variantes de mots](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) afin d’ajouter des synonymes à des mots clés qui prennent différentes formes. Les synonymes doivent être ajoutés au niveau du service QnA Maker et être partagés par toutes les bases de connaissances du service.

|Mot d’origine|Synonymes|
|--|--|
|buy|purchase<br>netbanking<br>net banking|

### <a name="use-distinct-words-to-differentiate-questions"></a>Utiliser des mots distincts pour différencier les questions
Les algorithmes de correspondance et de classement de QnA Maker recherchent une correspondance entre une requête utilisateur et l’une des questions contenues dans la base de connaissances. Ils retournent de meilleurs résultats si chaque question répond à un besoin différent. La répétition du même mot défini entre les questions réduit la probabilité de sélection de la réponse appropriée pour une requête utilisateur qui contient ces mots. 

Par exemple, vous pouvez avoir deux entités QnA distinctes avec les questions suivantes :

|Entités QnA|
|--|
|où est l’*emplacement* de stationnement|
|où est l’*emplacement* du distributeur automatique|

Ces deux entités QnA employant des mots très similaires, elles pourraient générer des scores très similaires pour de nombreuses requêtes d’utilisateur utilisant la tournure de phrase *« où est l’emplacement de `<x>` ».* Au lieu de cela, essayez de distinguer clairement avec des requêtes comme *« où est le parc de stationnement »* et *« où est le distributeur automatique »*, en évitant des mots tels que « emplacement » susceptibles de figurer dans de nombreuses questions dans votre base de connaissances. 


## <a name="collaborate"></a>Travailler en collaboration
QnA Maker permet aux utilisateurs de [travailler en collaboration](../How-to/collaborate-knowledge-base.md) sur une base de connaissances. Les utilisateurs doivent avoir accès au groupe de ressources Azure QnA Maker pour accéder à la base de connaissances. Certaines organisations souhaitent externaliser la mise à jour et la gestion des bases de connaissances tout en pouvant quand même protéger l’accès à leurs ressources Azure. Ce modèle éditeur-approbateur s’obtient en configurant deux [services QnA Maker](../How-to/set-up-qnamaker-service-azure.md) identiques dans des abonnements différents et en sélectionnant l’un d’entre eux pour le cycle de modification-test. À la fin des tests, le contenu de la base de connaissances est transféré, par un processus [importation-exportation](../Tutorials/migrate-knowledge-base.md), au service QnA Maker de l’approbateur, qui publiera la base de connaissances et mettra à jour le point de terminaison.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Modifier une base de connaissances](../How-to/edit-knowledge-base.md)
