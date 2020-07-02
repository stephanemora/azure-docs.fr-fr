---
title: 'Démarrage rapide : Essayer Content Moderator sur le web - Content Moderator'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez utiliser l’outil de révision Content Moderator pour tester les fonctionnalités de base de Content Moderator sans avoir à écrire du code.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 06/29/2020
ms.author: pafarley
ms.openlocfilehash: 550cec535343d48ad74dd35e6886af78bdd15701
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2020
ms.locfileid: "85563289"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Démarrage rapide : Essayer Content Moderator sur le web

Dans ce guide de démarrage rapide, vous allez utiliser l’outil de révision Content Moderator pour tester les fonctionnalités de base de Content Moderator sans avoir à écrire du code. Si vous souhaitez intégrer ce service dans votre application plus rapidement, consultez les autres démarrages rapides dans la section [Étapes suivantes](#next-steps).

## <a name="prerequisites"></a>Prérequis

- Un navigateur web

## <a name="set-up-the-review-tool"></a>Installer l’outil de révision
L’outil de révision Content Moderator est un outil web qui permet aux réviseurs humains d’aider le service cognitif à prendre des décisions. Dans ce guide, vous allez suivre le processus consistent à installer l’outil de révision pour voir comment le service Content Moderator fonctionne. Accédez au site de l’[outil de révision Content Moderator](https://contentmoderator.cognitive.microsoft.com/) et inscrivez-vous.

![Page d'accueil Content Moderator](images/homepage.PNG)

## <a name="create-a-review-team"></a>Créer une équipe de révision

Ensuite, créez une équipe de révision. Dans un scénario réel, il s’agit du groupe de personnes qui vérifie manuellement les décisions de modération du service. Pour créer une équipe, vous devez sélectionner une **Région** et spécifier un **Nom d’équipe** et un **ID d’équipe**. Si vous souhaitez intégrer des collègues dans l’équipe, entrez leurs adresses e-mail ici.

> [!NOTE]
> **Nom de l’équipe** est un nom convivial pour votre équipe de revue. Il s’agit du nom affiché dans le portail Azure. L’**ID de l’équipe** est utilisé pour identifier l’équipe de revue par programmation.

> [!div class="mx-imgBorder"]
> ![Inviter un membre d’équipe](images/create-team.png)

Si vous choisissez de chiffrer les données à l’aide d’une clé gérée par le client (CMK), vous êtes invité à fournir l’**ID de ressource** de votre ressource Content Moderator dans le niveau tarifaire E0. La ressource que vous fournissez doit être nouvelle. 

> [!div class="mx-imgBorder"]
> ![Inviter un membre de l’équipe avec une clé CMK](images/create-team-cmk.png)

Si vous essayez de réutiliser une ressource Content Moderator, cet avertissement s’affiche : 

> [!div class="mx-imgBorder"]
> ![Échec de la clé CMK](images/create-team-cmk-fail.png)

## <a name="upload-sample-content"></a>Charger un contenu exemple

Vous êtes maintenant prêt à charger un contenu exemple. Sélectionnez **Essayer > Image**, **Essayer > Texte** ou **Essayer > Vidéo**.

![Modération - Essayer image ou texte](images/tryimagesortext.png)

Envoyez votre contenu en modération. En interne, l’outil de révision appelle les API de modération et analyse votre contenu. Une fois l’analyse terminée, un message vous informe que des résultats sont en attente de révision.

![Modérer les fichiers](images/submitted.png)

## <a name="review-moderation-tags"></a>Vérifier les balises de modération

Passez en revue les balises de modération appliquées. Vous voyez les balises qui ont été appliquées à votre contenu et le score dans chaque catégorie. Pour en savoir plus sur ce qu'indiquent les différentes balises de contenu, consultez [Image](image-moderation-api.md), [Texte](text-moderation-api.md) et [Vidéo](video-moderation-api.md).

![Passer en revue les résultats](images/reviewresults_text.png)

Dans un projet, vous ou votre équipe de révision pouvez modifier ces balises ou, au besoin, en ajouter d’autres. Vous allez valider ces modifications en cliquant sur le bouton **Suivant**. Lorsque votre application appelle les API de modération, le contenu balisé est mis en file d’attente ici, où il attend la révision des personnes chargées de cette tâche. Cette approche vous permet de réviser rapidement d’importants volumes de contenu.

À ce stade, vous avez utilisé l’outil de révision Content Moderator pour voir ce que peut faire le service Content Moderator. Maintenant, vous pouvez soit en apprendre plus sur l’outil de révision et comment l’intégrer dans un projet de logiciel à l’aide des API de révision, soit passer à la section [Étapes suivantes](#next-steps) pour apprendre à utiliser les API de modération dans votre application.

## <a name="learn-more-about-the-review-tool"></a>En savoir plus sur l’outil de révision

Pour en savoir plus sur l’utilisation de l’outil de révision Content Moderator, consultez le guide [Outil de révision](Review-Tool-User-Guide/human-in-the-loop.md) et examinez les API de l’outil de révision pour savoir comment affiner la tâche de révision humaine :
- [L’API Travail](try-review-api-job.md) analyse votre contenu à l’aide des API de modération et génère des révisions dans l’outil de révision. 
- [L’API Révision](try-review-api-review.md) crée directement des révisions de vidéos, de textes et d’images pour les modérateurs humains sans analyse du contenu au préalable. 
- [L’API Flux de travail](try-review-api-workflow.md) crée et met à jour des flux de travail personnalisés créés par votre équipe, et obtient des informations sur ceux-ci.

Vous pouvez également passer aux étapes suivantes pour apprendre à utiliser les API de modération dans votre code.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser les API de modération dans votre application.
- Implémentez la modération d’images. Utilisez la [console d’API](try-image-api.md) ou suivez le [guide de démarrage rapide du SDK .NET](dotnet-sdk-quickstart.md) pour analyser des images et détecter tout contenu potentiellement pour adultes ou choquant à l’aide d’étiquettes, d’indices de confiance et d’autres informations extraites.
- Implémentez la modération de texte. Utilisez la [console d’API](try-text-api.md) ou le [guide de démarrage rapide du SDK .NET](dotnet-sdk-quickstart.md) pour rechercher dans un texte des termes vulgaires, du texte indésirable (fonctionnalité de classification de texte assistée par ordinateur, en préversion) et des données personnelles.
- Implémentez la modération de vidéo. Suivez le [guide de modération vidéo pour C# ](video-moderation-api.md) afin d'analyser des vidéos et détecter tout contenu potentiellement pour adultes et choquant. 
