---
title: Bien démarrer avec Content Moderator
titlesuffix: Azure Cognitive Services
description: Découvrez comment bien démarrer avec Content Moderator.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: sajagtap
ms.openlocfilehash: c2ac0ccd89b5f1436a151e3d69c5d7423090f244
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225292"
---
# <a name="get-started-with-content-moderator"></a>Bien démarrer avec Content Moderator

Vous pouvez démarrer avec Content Moderator des façons suivantes :

- [Utilisez d’abord l’outil de révision](#start-with-the-review-tool) pour obtenir la clé API et créer une équipe de révision. L’avantage de cette méthode est que vous pouvez ensuite utiliser la clé API pour appeler les API de modération (analyse du contenu) et les API de révision (génération des révisions) sans étapes supplémentaires.
- [Abonnez-vous à Content Moderator](#start-with-the-apis) dans Azure pour obtenir la clé API. Consultez les [informations de référence sur les API](api-reference.md) et les [SDK](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net). Vous devez vous inscrire en ligne pour créer une équipe de révision.
- [Utilisez les modèles et le connecteur Flow](https://flow.microsoft.com/connectors/shared_cognitiveservicescontentmoderator/content-moderator/) pour extraire un large éventail d’intégrations avec un concepteur facile à utiliser.

Quelle que soit l’option choisie, consultez l’article [Manage credentials](review-tool-user-guide/credentials.md) (Gérer des informations d'identification) pour rechercher vos informations d’identification d’API.

## <a name="start-with-the-review-tool"></a>Commencer par utiliser l’outil de révision
[Inscrivez-vous](http://contentmoderator.cognitive.microsoft.com/) sur le site web de l’outil de révision Content Moderator.

![Page d'accueil Content Moderator](images/homepage.PNG)

### <a name="create-a-review-team"></a>Créer une équipe de révision
Donnez un nom à votre équipe. Si vous souhaitez inviter vos collègues, vous pouvez le faire en entrant leurs adresses e-mail.

![Inviter un membre d’équipe](images/QuickStart-2-small.png)

### <a name="upload-images-or-enter-text"></a>Charger des images ou saisir du texte
Cliquez sur **Essayer > Image** ou **Essayer > Texte**. Chargez cinq exemples d’images maximum ou saisissez du texte pour la modération.

![Modération - Essayer image ou texte](images/tryimagesortext.png)

### <a name="submit-for-automated-moderation"></a>Envoyer pour modération automatisée
Envoyez votre contenu pour modération automatisée. En interne, l’outil de révision appelle les API de modération pour analyser votre contenu. Une fois l’analyse terminée, vous voyez un message vous informant sur les résultats en attente de révision.

![Modérer les fichiers](images/submitted.png)

### <a name="review-and-confirm-results"></a>Réviser et confirmer les résultats
Révisez les balises modérées automatiquement, modifiez-les si besoin et envoyez-les en cliquant sur le bouton **Suivant**. À mesure que votre application appelle les API Moderator, le contenu balisé commence à être placé en file d’attente, prêt à être révisé par les équipes de révision humaines. Cette approche vous permet de réviser rapidement d’importants volumes de contenu.

![Passer en revue les résultats](images/reviewresults.png)

Découvrez comment utiliser toutes les [fonctionnalités de l’outil de révision](Review-Tool-User-Guide/human-in-the-loop.md) ou passez à la section suivante pour en apprendre davantage sur les API. Vous pouvez ignorer l’étape d’inscription, car les clés API ont déjà été provisionnées pour vous dans l’outil de révision, comme expliqué dans l’article [Manage credentials](review-tool-user-guide/credentials.md) (Gérer des informations d'identification).

### <a name="use-the-apis"></a>Utiliser les API

Découvrez comment intégrer Content Moderator à vos applications métier. Consultez les [informations de référence sur les API](api-reference.md) et les [SDK](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net).

## <a name="subscribe-in-the-azure-portal"></a>S’abonner dans le portail Azure

Dans le Portail Azure, [abonnez-vous à Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator). Commencez avec l’une des API suivantes :

### <a name="image-moderation"></a>Modération d’image

Commencez avec la [console d’API](try-image-api.md) ou utilisez le [démarrage rapide .NET](image-moderation-quickstart-dotnet.md) pour analyser des images et détecter tout contenu potentiellement pour adultes ou choquant à l’aide de balises, d’indices de confiance et d’autres informations extraites.

### <a name="text-moderation"></a>Modération de texte

Commencez avec la [console d’API](try-text-api.md) ou utilisez le [démarrage rapide .NET](text-moderation-quickstart-dotnet.md) pour analyser du contenu de texte comprenant potentiellement des termes vulgaires, la classification de texte non sollicité assistée par ordinateur (préversion) et des informations d’identification personnelle. 


### <a name="video-moderation"></a>Modération de vidéo

Commencez avec le [démarrage rapide .NET](video-moderation-api.md) pour analyser des vidéos et détecter tout contenu potentiellement pour adultes ou choquant. 


### <a name="review-apis"></a>Consultation des API

Démarrez ici en choisissant parmi les API Travail, Révision et Flux de travail.

- [L’API Travail](try-review-api-job.md) analyse votre contenu à l’aide des API de modération et génère des révisions dans l’outil de révision. 
- [L’API Révision](try-review-api-review.md) crée directement des révisions de vidéos, de textes et d’images pour les modérateurs humains sans analyse du contenu au préalable. 
- [L’API Flux de travail](try-review-api-workflow.md) crée et met à jour des flux de travail personnalisés créés par votre équipe, et obtient des informations sur ceux-ci.

## <a name="next-steps"></a>Étapes suivantes

Consultez les [informations de référence sur les API](api-reference.md) et les [SDK](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net). Commencez votre intégration à l’aide des [exemples du SDK .NET](sdk-and-samples.md#net-sdk-samples), des [exemples d’API REST en C#](https://github.com/sanjeev3/azure-docs-pr/blob/master/articles/cognitive-services/Content-Moderator/sdk-and-samples.md#rest-api-samples-in-c) et des [tutoriels](sdk-and-samples.md#tutorials).
