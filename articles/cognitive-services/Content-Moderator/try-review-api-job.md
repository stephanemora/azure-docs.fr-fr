---
title: Utilisation des tâches de modération avec la console de l’API REST - Content Moderator
titlesuffix: Azure Cognitive Services
description: Utilisez les opérations de travail de l’API Review (Révision) pour initier des travaux de modération du contenu de bout en bout pour le contenu d’image ou de texte dans Azure Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 7827cee2af2dfc0c1fddc407c1d146dc9a66c514
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607580"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Définir et utiliser des travaux de modération (REST)

Un travail de modération sert un type de wrapper pour la fonctionnalité de modération du contenu, les workflows et les révisions. Ce guide vous montre comment utiliser la tâche API REST pour lancer et contrôler les tâches de modération du contenu. Une fois que vous comprenez la structure des API, vous pouvez facilement porter ces appels à n’importe quelle plateforme compatible avec REST.

## <a name="prerequisites"></a>Conditions préalables

- Connectez-vous ou créez un compte sur le modérateur de contenu [outil de révision](https://contentmoderator.cognitive.microsoft.com/) site.
- (Facultatif) [Définir un flux de travail personnalisé](./Review-Tool-User-Guide/Workflows.md) à utiliser avec votre travail ; vous pouvez également utiliser le flux de travail par défaut.

## <a name="create-a-job"></a>Création d’un travail

Pour créer un travail de modération, accédez à la [du travail - créer](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API référence la page, sélectionnez le bouton pour votre région clé (vous pouvez le trouver dans l’URL de point de terminaison sur le **informations d’identification** page de la [révision outil](https://contentmoderator.cognitive.microsoft.com/)). Cela démarre la console d’API, où vous pouvez facilement construire et exécuter des appels d’API REST.

![Travail - créer de sélection de zone de page](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Entrez les paramètres d’appel REST

Entrez les valeurs suivantes pour construire l’appel REST :

- **teamName** : ID d’équipe que vous avez créé lorsque vous configurez votre [outil de révision](https://contentmoderator.cognitive.microsoft.com/) compte (trouvée dans le **Id** champ sur l’écran des informations d’identification de votre outil de révision).
- **ContentType**: Cela peut être « Image », « Text » ou « Video ».
- **ContentId** : Une chaîne d’identificateur personnalisé. cette chaîne est transmise à l'API et retournée via le rappel. Il est utile pour associer des identificateurs internes ou des métadonnées avec les résultats d’une tâche de modération.
- **workflowname**: Le nom du flux de travail que vous avez créé précédemment (ou « default » pour le flux de travail par défaut).
- **CallbackEndpoint**: (Facultatif) L’URL de recevoir des informations de rappel lorsque la révision est terminée.
- **Ocp-Apim-Subscription-Key** : Votre clé de Content Moderator. Vous pouvez le trouver sur le **paramètres** onglet de la [outil de vérification](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Renseignez le corps de la demande

Le corps de votre appel REST contient un champ, **ContentValue**. Collez le contenu de texte brut si vous modération de texte, ou entrez une image ou une URL de la vidéo si vous êtes modération d’image/vidéo. Vous pouvez utiliser l’URL d’image exemple suivante : [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Paramètres de la requête, en-têtes et zone Corps de la demande dans la console Travail - Créer](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Soumettre votre requête

Sélectionnez **Envoyer**. Si l’opération réussit, le **état de la réponse** est `200 OK`et le **contenu de la réponse** zone affiche un ID pour le travail. Copiez cet ID pour l’utiliser dans les étapes suivantes.

![La zone Contenu de la réponse de la console Review - Create (Révision - Créer) affiche l’ID de révision](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Obtenir l’état de la tâche

Pour obtenir l’état et les détails d’une tâche en cours d’exécution ou terminée, accédez à la [du travail - obtenir](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) API référence la page et sélectionnez le bouton de votre région (la région dans laquelle votre clé est administrée).

![Travail - sélection de la région Get](images/test-drive-region.png)

Entrez les paramètres d’appel REST, comme dans la section ci-dessus. Pour cette étape, **JobId** est la chaîne d’ID unique que vous avez reçu lorsque vous avez créé la tâche. Sélectionnez **Envoyer**. Si l’opération réussit, le **état de la réponse** est `200 OK`et le **contenu de la réponse** boîte affiche le travail au format JSON, comme suit :

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![Travail - obtenir une réponse d’appel REST](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Examiner la nouvelle review(s)

Si votre travail contenu a entraîné la création d’une révision, vous pouvez l’afficher dans le [outil de vérification](https://contentmoderator.cognitive.microsoft.com). Sélectionnez **révision** > **Image**/**texte**/**vidéo** (selon le contenu que vous utilisé). Le contenu doit apparaître, prêt pour la révision manuelle. Une fois un modérateur humain examine les balises assignées automatiquement et les données de prédiction et soumet une décision finale modération, l’API de tâches soumet toutes ces informations pour le point de terminaison de point de terminaison de rappel désigné.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide, vous avez appris à créer et interroger des travaux de modération du contenu à l’aide de l’API REST. Ensuite, intégrez des travaux à un scénario de modération de bout en bout, telles que la [modération de commerce électronique](./ecommerce-retail-catalog-moderation.md) didacticiel.