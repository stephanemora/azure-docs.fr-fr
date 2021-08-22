---
title: Découpage d’une vidéo lors de l’encodage avec Media Services REST
description: Cette rubrique décrit comment sous-découper une vidéo lors de l’encodage avec Azure Media Services à l’aide de l’API REST
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 06/10/2019
ms.author: inhenkel
ms.openlocfilehash: 3f8712709f1160150275ee55d84522746bbe8a3a
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "122641205"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Sous-découper une vidéo lors de l’encodage avec Media Services - REST

Vous pouvez découper ou sous-découper une vidéo lors de son encodage à l’aide d’un [travail](/rest/api/media/jobs). Cette fonctionnalité peut être utilisée avec n’importe quelle [transformation](/rest/api/media/transforms) qui est générée en utilisant les présélections [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset). 

L’exemple REST de cette rubrique crée un travail qui découpe une vidéo à mesure qu’il envoie un travail d’encodage. 

[!INCLUDE [warning-rest-api-retry-policy.md](./includes/warning-rest-api-retry-policy.md)]


## <a name="prerequisites"></a>Prérequis

Pour suivre les étapes décrites dans cette rubrique, vous devez :

- [Créer un compte Azure Media Services](./account-create-how-to.md).
- [Configurer Postman pour les appels d’API REST Azure Media Services](setup-postman-rest-how-to.md).
    
    Suivez la dernière étape de la rubrique [Obtenir un jeton Azure AD](setup-postman-rest-how-to.md#get-azure-ad-token). 
- Créez une transformation, ainsi que les éléments multimédias de sortie. Pour savoir comment créer une transformation et des éléments multimédias de sortie, consultez le didacticiel [Encoder un fichier distant basé sur une URL et diffuser la vidéo en continu - REST](stream-files-tutorial-with-rest.md).
- Consultez la rubrique dédiée au [concept d’encodage](encode-concept.md).

## <a name="create-a-subclipping-job"></a>Créer un travail de sous-découpage

1. Dans la collection Postman que vous avez téléchargée, sélectionnez **Transformations et travaux** -> **Create Job with Sub Clipping** (Créer un travail avec sous-découpage).
    
    La requête **PUT** ressemble à celle-ci :
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Mettez à jour la valeur de variable d’environnement « transformName » avec le nom de votre transformation. 
1. Sélectionnez l’onglet **Corps** onglet et mettez à jour « myOutputAsset » avec le nom de votre élément multimédia de sortie.

    ```json
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
            "Ignite-short.mp4"
          ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. Appuyez sur **Envoyer**.

    Vous voyez la **réponse** avec les informations sur le travail créé et envoyé, et l’état de celui-ci. 

## <a name="next-steps"></a>Étapes suivantes

[Comment encoder avec une transformation personnalisée](transform-custom-preset-rest-how-to.md) 
