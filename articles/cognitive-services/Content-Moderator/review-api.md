---
title: Révisions, flux de travail et les concepts de travaux - Content Moderator
titlesuffix: Azure Cognitive Services
description: En savoir plus sur les révisions, les flux de travail et les travaux
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: c1d4ef640e2ae072dacba7a665b6689e3224c55c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607294"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Révisions de modération du contenu, les flux de travail et les travaux

Content Moderator associe de modération assistée par ordinateur avec les capacités humaines en boucle pour créer un processus de modération optimal pour les scénarios réels. Pour cela, via le nuage [outil de vérification](https://contentmoderator.cognitive.microsoft.com). Dans ce guide, vous allez découvrir les concepts fondamentaux de l’outil de vérification : révisions, les flux de travail et les travaux.

## <a name="reviews"></a>Révisions

Dans une révision, le contenu est chargé dans l’outil de vérification et apparaît sous le **Examinez** onglet. À ce stade, les utilisateurs peuvent modifier les balises appliquées et s’appliquent leurs propres balises personnalisées comme il convient. Lorsqu’un utilisateur soumet une revue, les résultats sont envoyés à un point de terminaison de rappel spécifiée et le contenu est supprimé à partir du site.

![Site Web outil de révision ouvrir dans un navigateur, sous l’onglet Révision](./Review-Tool-user-Guide/images/image-workflow-review.png)

Consultez le [guide l’outil de révision](./review-tool-user-guide/review-moderated-images.md) pour commencer à créer des révisions, ou consultez le [guide de l’API REST](./try-review-api-review.md) pour apprendre à le faire par programmation.

## <a name="workflows"></a>Flux de travail

Un flux de travail est un filtre personnalisé basé sur le cloud pour le contenu. Flux de travail peut se connecter à une variété de services pour filtrer le contenu de différentes façons et puis exécutez l’action appropriée. Avec le connecteur Content Moderator, un flux de travail peut automatiquement appliquer des balises de modération et créez révisions avec contenu soumis.

### <a name="view-workflows"></a>Affichage des workflows

Pour afficher vos flux de travail existant, accédez à la [outil de révision](https://contentmoderator.cognitive.microsoft.com/) et sélectionnez **paramètres** > **des flux de travail**.

![Flux de travail par défaut](images/default-workflow-listed.PNG)

Flux de travail peut être décrit complètement en tant que chaînes JSON, ce qui les rend accessibles par programmation. Si vous sélectionnez le **modifier** option pour votre flux de travail, puis sélectionnez le **JSON** onglet, vous verrez une expression JSON comme suit :

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

Consultez le [guide l’outil de révision](./review-tool-user-guide/workflows.md) pour commencer la création et l’utilisation des flux de travail, ou consultez le [guide de l’API REST](./try-review-api-workflow.md) pour apprendre à le faire par programmation.

## <a name="jobs"></a>Tâches

Un travail de modération sert un type de wrapper pour la fonctionnalité de modération du contenu, les workflows et les révisions. La tâche analyse votre contenu à l’aide de la modération de l’image modérateur de contenu API ou API de modération de texte et il vérifie ensuite sur le workflow désigné. Selon les résultats de flux de travail, il peut ou ne peut pas créer une révision pour le contenu dans le [outil de vérification](./review-tool-user-guide/human-in-the-loop.md). Tandis que révisions et flux de travail peut être créé et configuré avec leurs API respectifs, la tâche API permet de vous permettent d’obtenir un rapport détaillé de l’ensemble du processus (qui peuvent être envoyé à un point de terminaison de rappel spécifiés).

Consultez le [guide de l’API REST](./try-review-api-job.md) pour commencer à utiliser des travaux.

## <a name="next-steps"></a>Étapes suivantes

* Testez la [console d’API Job](try-review-api-job.md) et utilisez les exemples de code API REST. Si vous connaissez déjà Visual Studio et C#, consultez aussi le [guide de démarrage des tâches .NET](moderation-jobs-quickstart-dotnet.md). 
* Pour les révisions, prenez en main la [console d’API Révision](try-review-api-review.md), et utilisez les exemples de code de l’API REST. Consultez ensuite le [guide de démarrage rapide des révisions .NET](moderation-reviews-quickstart-dotnet.md).
* Pour des révisions vidéo, utilisez le [guide de démarrage rapide pour révision vidéo](video-reviews-quickstart-dotnet.md), et apprenez à [ajouter des transcriptions à la révision vidéo](video-transcript-reviews-quickstart-dotnet.md).
