---
title: Travaux de modération et révisions manuelles - Content Moderator
titlesuffix: Azure Cognitive Services
description: Alliez une modération assistée par ordinateur avec une intervention humaine à l’aide de l’API Révision d’Azure Content Moderator pour obtenir les meilleurs résultats possibles pour votre entreprise.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 21d71110853c5f18b0b5f0b51d30110eb45ff54a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55862698"
---
# <a name="content-moderation-jobs-and-reviews"></a>Tâches de modération et révisions

Alliez une modération assistée par ordinateur avec une intervention humaine à l’aide de [l’API Révision](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) d’Azure Content Moderator pour obtenir les meilleurs résultats possibles pour votre entreprise.

L’API de révision propose les méthodes suivantes pour inclure une supervision humaine dans votre processus de modération du contenu :

* Les opérations `Job` sont utilisées pour le démarrage d’une modération assistée par ordinateur et la création de révisions par un opérateur humain en une seule étape.
* Les opérations `Review` sont utilisées pour la création de révisions par un opérateur humain, en dehors de l’étape de modération.
* Les opérations `Workflow` sont utilisées pour la gestion des flux de travail qui automatisent l’analyse avec des seuils pour la création de révisions.

Les opérations `Job` et `Review` acceptent vos points de terminaison de rappel pour la réception d’états et de résultats.

Cet article aborde les opérations `Job` et `Review`. Consultez la [Présentations des workflows](workflow-api.md) pour en savoir plus sur la création, la modification et l’obtention de définitions de workflows.

## <a name="job-operations"></a>Opérations de travail

### <a name="start-a-job"></a>Démarrer une tâche
Utilisez l’opération `Job.Create` pour démarrer une tâche de création d’une modération et d’une révision par un opérateur humain. Content Moderator analyse le contenu et évalue le workflow désigné. En fonction des résultats du workflow, il crée des révisions ou ignore l’étape. Il envoie aussi les balises de post-modération et post-révision à votre point de terminaison de rappel.

Les entrées incluent les informations suivantes :

- ID de l’équipe de révision.
- Le contenu à modérer.
- Le nom du workflow. (Le workflow par défaut est « default ».)
- Votre point de terminaison de rappel d’API pour les notifications.
 
La réponse suivante affiche l’identificateur de la tâche qui a été démarrée. Vous utilisez l’identificateur de la tâche pour obtenir son état et recevoir des informations détaillées.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

### <a name="get-job-status"></a>Obtenir l’état de la tâche

Utilisez l’opération `Job.Get` et l’identificateur de la tâche pour obtenir les détails d’une tâche en cours d’exécution ou accomplie. L’opération retourne immédiatement tandis que la tâche de modération s’exécute de façon asynchrone. Les résultats sont retournés par le biais du point de terminaison de rappel.

Vos entrées incluent les informations suivantes :

- ID de l’équipe de révision : L’identificateur de la tâche renvoyé par l’opération précédente

La réponse inclut les informations suivantes :

- L’identificateur de la révision créée. (Utilisez cet ID pour obtenir les résultats finaux de la révision.)
- L’état de la tâche (terminée ou en cours) : Les balises de modération affectées (paires clé-valeur).
- Le rapport d’exécution de la tâche.
 
 
        {
            "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
            "TeamName": "some team name",
            "Status": "Complete",
            "WorkflowId": "OCR",
            "Type": "Image",
            "CallBackEndpoint": "",
            "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
            "ResultMetaData":[
            {
            "Key": "hasText",
            "Value": "True"
            },
            {
            "Key": "ocrText",
            "Value": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
            }
            ],
            "JobExecutionReport": [
            {
                "Ts": "2018-01-07T00:38:29.3238715",
                "Msg": "Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
                },
                {
                "Ts": "2018-01-07T00:38:29.2928416",
                "Msg": "Job marked completed and job content has been removed"
                },
                {
                "Ts": "2018-01-07T00:38:29.0856472",
                "Msg": "Execution Complete"
                },
            {
                "Ts": "2018-01-07T00:38:26.7714671",
                "Msg": "Successfully got hasText response from Moderator"
                },
                {
                "Ts": "2018-01-07T00:38:26.4181346",
                "Msg": "Getting hasText from Moderator"
                },
                {
                "Ts": "2018-01-07T00:38:25.5122828",
                "Msg": "Starting Execution - Try 1"
                }
            ]
        }
 
![Révision d’images pour les modérateurs humains](images/ocr-sample-image.PNG)

## <a name="review-operations"></a>Opérations de révision

### <a name="create-reviews"></a>Créer des révisions

Utilisez l’opération `Review.Create` pour créer les révisions par un opérateur humain. Vous les modérez ailleurs ou utilisez une logique personnalisée pour assigner les balises de modération.

Vos entrées pour cette opération incluent :

- Le contenu à réviser.
- Les balises assignées (paires clé-valeur) à réviser par des opérateurs humains.

La réponse suivante affiche l’identificateur de la révision :

    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]


### <a name="get-review-status"></a>Obtenir l’état de révision
Utilisez l’opération `Review.Get` pour obtenir les résultats après révision par un opérateur humain de l’image modérée. Vous êtes notifié via le point de terminaison de rappel que vous avez fourni. 

L’opération retourne deux ensembles de balises : 

* Les balises assignées par le service de modération
* Les balises obtenues après révision par un opérateur humain

Vos entrées incluent au minimum :

- Le nom de l’équipe de révision
- L’identificateur de la révision renvoyé par l’opération précédente

La réponse inclut les informations suivantes :

- État de révision
- Les balises (paires clé-valeur), confirmées par le réviseur humain
- Les balises (paires clé-valeur) assignées par le service de modération

Vous voyez les balises assignées par le réviseur (**reviewerResultTags**) et les balises d’origine (**metadata**) dans l’exemple de réponse suivant :

    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
            "key": "a",
            "value": "False"
        },
        {
            "key": "r",
            "value": "True"
        },
        {
            "key": "sc",
            "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

## <a name="next-steps"></a>Étapes suivantes

* Testez la [console d’API Job](try-review-api-job.md) et utilisez les exemples de code API REST. Si vous connaissez déjà Visual Studio et C#, consultez aussi le [guide de démarrage des tâches .NET](moderation-jobs-quickstart-dotnet.md). 
* Pour les révisions, prenez en main la [console d’API Révision](try-review-api-review.md), et utilisez les exemples de code de l’API REST. Consultez ensuite le [guide de démarrage rapide des révisions .NET](moderation-reviews-quickstart-dotnet.md).
* Pour des révisions vidéo, utilisez le [guide de démarrage rapide pour révision vidéo](video-reviews-quickstart-dotnet.md), et apprenez à [ajouter des transcriptions à la révision vidéo](video-transcript-reviews-quickstart-dotnet.md).
