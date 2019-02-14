---
title: Exécuter des travaux de modération du contenu avec la console d’API - Content Moderator
titlesuffix: Azure Cognitive Services
description: Utilisez les opérations de travail de l’API Review (Révision) pour initier des travaux de modération du contenu de bout en bout pour le contenu d’image ou de texte dans Azure Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 9fac80ff152b0f7b9c36c182759d41245364fff9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55862426"
---
# <a name="start-a-moderation-job-from-the-api-console"></a>Démarrer un travail de modération à partir de la console d’API

Utilisez les [opérations de travail](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) de l’API Review (Révision) pour initier des travaux de modération du contenu de bout en bout pour le contenu d’image ou de texte dans Azure Content Moderator. 

Le travail de modération analyse votre contenu à l’aide de l’API Modération des images ou de l’API Modération du texte de Content Moderator. Le travail de modération utilise ensuite des flux de travail (définis dans l’outil de révision) pour générer des révisions dans l’outil de révision. 

Une fois qu’un modérateur a examiné les balises affectées automatiquement ainsi que les données de prédiction et soumis une décision finale en matière de modération, l’API Review (Révision) soumet toutes les informations à votre point de terminaison d’API.

## <a name="prerequisites"></a>Prérequis

Accédez à [l’outil de révision](https://contentmoderator.cognitive.microsoft.com/). Inscrivez-vous si ce n’est pas encore fait. Dans l’outil de révision, [définissez un flux de travail personnalisé](Review-Tool-User-Guide/Workflows.md) à utiliser dans cette opération `Job`.

## <a name="use-the-api-console"></a>Utiliser la console d’API
Pour évaluer l’API à l’aide de la console en ligne, vous devez entrer quelques valeurs dans la console :
    
- `teamName`: utilisez le champ `Id` de l’écran des informations d’identification de l’outil de révision. 
- `ContentId`: cette chaîne est transmise à l'API et retournée via le rappel. **ContentId** est utile pour associer des métadonnées ou des identificateurs internes aux résultats d’un travail de modération. - `Workflowname` : nom du [workflow que vous avez créé ](Review-Tool-User-Guide/Workflows.md)dans la section précédente.
- `Ocp-Apim-Subscription-Key`: valeur située dans l'onglet **Paramètres**. Pour plus d’informations, consultez l’article de [présentation](overview.md).

L’accès à la console d’API se fait à partir de la fenêtre **Informations d’identification**.

### <a name="navigate-to-the-api-reference"></a>Accéder à la référence API
Dans la fenêtre **Informations d’identification**, sélectionnez [Référence API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5).

  La page `Job.Create` s’ouvre.

### <a name="select-your-region"></a>Sélectionner votre région
Pour l’option **Open API testing console** (Ouvrir la console de test de l’API), sélectionnez la région qui décrit le mieux votre emplacement.
  ![Sélection d’une région dans la page Travail - Créer](images/test-drive-job-1.png)

  La console de l’API `Job.Create` s’ouvre. 

### <a name="enter-parameters"></a>Saisie des paramètres

Entrez des valeurs pour les paramètres de requête requis, puis votre clé d’abonnement. Dans la zone **Corps de la demande**, spécifiez l’emplacement des informations que vous souhaitez analyser. Pour cet exemple, nous allons utiliser cet [exemple d’image](https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png).

  ![Paramètres de la requête, en-têtes et zone Corps de la demande dans la console Travail - Créer](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Soumettre votre requête
Sélectionnez **Envoyer**. Un ID de travail est créé. Copiez-le pour l’utiliser dans les étapes suivantes.

  `"JobId": "2018014caceddebfe9446fab29056fd8d31ffe"`

### <a name="open-the-get-job-details-page"></a>Ouvrir la page de détails Get Job (Obtenir le travail)
Sélectionnez **Obtenir**, puis ouvrez l’API en sélectionnant le bouton qui correspond à votre région.

  ![Obtention des résultats dans la console Travail - Créer](images/test-drive-job-4.png)

### <a name="review-the-response"></a>Passer en revue la réponse

Entrez les valeurs des paramètres **teamName** et **JobID**. Entrez votre clé d’abonnement, puis sélectionnez **Envoyer**. La réponse suivante illustre un exemple de détails et d’état d’un travail.

```
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": 
        [
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
```

## <a name="navigate-to-the-review-tool"></a>Accéder à l’outil de révision
Dans le tableau de bord Content Moderator, sélectionnez **Réviser** > **Image**. L’image que vous avez analysée s’affiche et peut être révisée.

  ![Image de l’outil de révision présentant trois cyclistes](images/ocr-sample-image.PNG)

## <a name="next-steps"></a>Étapes suivantes

Utilisez l’API REST dans votre code ou commencez par le [démarrage rapide de travaux .NET](moderation-jobs-quickstart-dotnet.md) pour en intégrer à votre application.
