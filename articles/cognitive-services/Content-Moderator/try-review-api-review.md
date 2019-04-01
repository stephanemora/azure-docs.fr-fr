---
title: Créer des révisions de modération avec la console de l’API REST - Content Moderator
titlesuffix: Azure Cognitive Services
description: API de révision Content Moderator Azure permet de créer des révisions d’image ou du texte pour la modération humaine.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: beadbfc09526f738ba90252787b5b0910a2f7163
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755362"
---
# <a name="create-human-reviews-rest"></a>Créer des révisions humain (REST)

[Révisions](./review-api.md#reviews) stocker et afficher le contenu pour les modérateurs humains à évaluer. Lorsqu’un utilisateur termine une revue, les résultats sont envoyés à un point de terminaison de rappel spécifié. Dans ce guide, vous allez apprendre à configurer des révisions à l’aide de la révision d’API REST via la console d’API. Une fois que vous comprenez la structure des API, vous pouvez facilement porter ces appels à n’importe quelle plateforme compatible avec REST.

## <a name="prerequisites"></a>Conditions préalables

- Connectez-vous ou créez un compte sur le modérateur de contenu [outil de révision](https://contentmoderator.cognitive.microsoft.com/) site.

## <a name="create-a-review"></a>Créer une révision

Pour créer une révision, accédez à la **[consulter : créer](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API référence la page, sélectionnez le bouton pour votre région clé (vous pouvez le trouver dans l’URL de point de terminaison sur le **informations d’identification** page de la [outil de révision](https://contentmoderator.cognitive.microsoft.com/)). Cela démarre la console d’API, où vous pouvez facilement construire et exécuter des appels d’API REST.

![Consulter : sélection de la région Get](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Entrez les paramètres d’appel REST

Entrez les valeurs **Nom_équipe**, et **Ocp-Apim-Subscription-Key**:

- **teamName** : ID d’équipe que vous avez créé lorsque vous configurez votre [outil de révision](https://contentmoderator.cognitive.microsoft.com/) compte (trouvée dans le **Id** champ sur l’écran des informations d’identification de votre outil de révision).
- **Ocp-Apim-Subscription-Key** : Votre clé de Content Moderator. Vous pouvez le trouver sur le **paramètres** onglet de la [outil de vérification](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Entrez une définition de la révision

Modifier le **corps de la demande** zone pour entrer la requête JSON avec les champs suivants :

- **Métadonnées** : Paires clé-valeur personnalisées à retourner à votre point de terminaison de rappel. Si la clé est un code court qui est défini dans le [outil de révision](https://contentmoderator.cognitive.microsoft.com), il s’agit d’une balise.
- **Contenu**: Dans le cas de contenu d’Image et vidéo, il s’agit d’une chaîne d’URL qui pointe vers le contenu. Pour le contenu de texte, il s’agit de la chaîne de texte réelle.
- **ContentId** : Une chaîne d’identificateur personnalisé. cette chaîne est transmise à l'API et retournée via le rappel. Il est utile pour associer des identificateurs internes ou des métadonnées avec les résultats d’une tâche de modération.
- **CallbackEndpoint**: (Facultatif) L’URL de recevoir des informations de rappel lorsque la révision est terminée.

Le corps de la demande par défaut présente des exemples des différents types de révisions, que vous pouvez créer :

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>Soumettre votre requête
  
Sélectionnez **Envoyer**. Si l’opération réussit, le **état de la réponse** est `200 OK`et le **contenu de la réponse** zone affiche un ID pour la révision. Copiez cet ID pour l’utiliser dans les étapes suivantes.

![La zone Contenu de la réponse de la console Review - Create (Révision - Créer) affiche l’ID de révision](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Examiner la nouvelle révision

Dans le [outil de révision](https://contentmoderator.cognitive.microsoft.com), sélectionnez **révision** > **Image**/**texte** / **Vidéo** (selon le contenu que vous avez utilisé). Le contenu que vous avez téléchargé doit apparaître, prêt pour la révision manuelle.

![Image de l’outil de révision présentant un ballon de football](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Obtenir les détails de révision

Pour extraire des informations sur une révision existante, accédez à la [consulter : obtenir](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) API référence la page et sélectionnez le bouton de votre région (la région dans laquelle votre clé est administrée).

![Flux de travail - Sélection de région](images/test-drive-region.png)

Entrez les paramètres d’appel REST, comme dans la section ci-dessus. Pour cette étape, **reviewId** est la chaîne d’ID unique que vous avez reçu lorsque vous avez créé la révision.

![Obtention des résultats dans la console Review - Create (Révision - Créer)](images/test-drive-review-3.PNG)
  
Sélectionnez **Envoyer**. Si l’opération réussit, le **état de la réponse** est `200 OK`et le **contenu de la réponse** zone affiche la les détails de révision au format JSON, comme suit :

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

Prenez note des champs suivants dans la réponse :

- **statut**
- **reviewerResultTags**: Cela s’affiche si toutes les balises ont été ajoutés manuellement par l’équipe de révision manuelle (illustré le **createdBy** champ).
- **metadata** : Cela montre les balises ont été initialement ajoutés lors de la révision, avant le changement de l’équipe de révision manuelle.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide, vous avez appris à créer des révisions de modération du contenu à l’aide de l’API REST. Ensuite, intégrer des révisions d’un scénario de modération de bout en bout, telles que la [modération de commerce électronique](./ecommerce-retail-catalog-moderation.md) didacticiel.