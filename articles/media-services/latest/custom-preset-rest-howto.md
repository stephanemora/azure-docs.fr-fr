---
title: Encoder une transformation personnalisée avec Media Services v3 REST - Azure | Microsoft Docs
description: Cette rubrique explique comment utiliser Azure Media Services v3 pour encoder une transformation personnalisée à l’aide de REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 30e22cb786e5dc2a667fe41ca8edf398cf0b7613
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "65761791"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Comment encoder avec une transformation personnalisée - REST

Lors de l’encodage avec Azure Media Services, vous pouvez commencer rapidement avec l’un des préréglages intégrés recommandés et basés sur les bonnes pratiques, comme illustré dans le didacticiel [Streaming de fichiers](stream-files-tutorial-with-rest.md#create-a-transform). Vous pouvez également créer un préréglage personnalisé pour les besoins de votre scénario ou votre appareil.

## <a name="considerations"></a>Considérations

Lorsque vous créez des préréglages personnalisés, les considérations suivantes s’appliquent :

* Toutes les valeurs de hauteur et de largeur de contenu AVC doivent être un multiple de 4.
* Dans Azure Media Services v3, toutes les vitesses d’encodage sont données en bits par seconde. Cela diffère des préréglages avec nos API v2, qui utilisaient des kilobits par seconde comme unité. Par exemple, si la vitesse de transmission dans v2 était de 128 (kilobits/seconde), elle sera définie sur 12 8000 (bits/seconde) dans v3.

## <a name="prerequisites"></a>Prérequis 

- [Créer un compte Media Services](create-account-cli-how-to.md). <br/>Veillez à mémoriser le nom du groupe de ressources et le nom du compte Media Services. 
- [Configurer Postman pour les appels d’API REST Azure Media Services](media-rest-apis-with-postman.md).<br/>Suivez la dernière étape de la rubrique [Obtenir un jeton Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Définir un préréglage personnalisé

L’exemple suivant définit le corps de la demande d’une nouvelle transformation. Nous définissons un ensemble de sorties à générer lorsque cette transformation est utilisée. 

Dans cet exemple, nous ajoutons tout d’abord une couche AacAudio pour l’encodage audio et deux couches H264Video pour l’encodage vidéo. Dans les couches vidéo, nous attribuons des étiquettes pour pouvoir les utiliser dans les noms de fichiers de sortie. Nous souhaitons ensuite que la sortie inclue également des miniatures. Dans l’exemple ci-dessous, nous spécifions des images au format PNG, générées à 50 % de la résolution de la vidéo d’entrée et à trois horodatages {25 %, 50 %, 75} de la longueur de la vidéo d’entrée. Pour fini, nous spécifions le format des fichiers de sortie : un pour la vidéo + audio et un autre pour les miniatures. Étant donné que nous avons plusieurs couches H264, nous devons utiliser des macros qui produisent des noms uniques par couche. Nous pouvons utiliser une macro `{Label}` ou `{Bitrate}` ; l’exemple montre la première.

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.AacAudio",
                            "channels": 2,
                            "samplingRate": 48000,
                            "bitrate": 128000,
                            "profile": "AacLc"
                        },
                        {
                            "@odata.type": "#Microsoft.Media.H264Video",
                            "keyFrameInterval": "PT2S",
                            "stretchMode": "AutoSize",
                            "sceneChangeDetection": false,
                            "complexity": "Balanced",
                            "layers": [
                                {
                                    "width": "1280",
                                    "height": "720",
                                    "label": "HD",
                                    "bitrate": 3400000,
                                    "maxBitrate": 3400000,
                                    "bFrames": 3,
                                    "slices": 0,
                                    "adaptiveBFrame": true,
                                    "profile": "Auto",
                                    "level": "auto",
                                    "bufferWindow": "PT5S",
                                    "referenceFrames": 3,
                                    "entropyMode": "Cabac"
                                },
                                {
                                    "width": "640",
                                    "height": "360",
                                    "label": "SD",
                                    "bitrate": 1000000,
                                    "maxBitrate": 1000000,
                                    "bFrames": 3,
                                    "slices": 0,
                                    "adaptiveBFrame": true,
                                    "profile": "Auto",
                                    "level": "auto",
                                    "bufferWindow": "PT5S",
                                    "referenceFrames": 3,
                                    "entropyMode": "Cabac"
                                }
                            ]
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "25%",
                            "step": "25%",
                            "range": "80%",
                            "layers": [
                                {
                                    "width": "50%",
                                    "height": "50%"
                                }
                            ]
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
                            "outputFiles": []
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngFormat",
                            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="create-a-new-transform"></a>Créer une transformation  

Dans cet exemple, nous créons une **transformation** qui repose sur le préréglage personnalisé que nous avons défini précédemment. Lorsque vous créez une transformation, vous devez tout d’abord vérifier s’il en existe déjà une à l’aide de la méthode [Get](https://docs.microsoft.com/rest/api/media/transforms/get). Si la transformation existe, réutilisez-la. 

Dans la collection de Postman que vous avez téléchargée, sélectionnez **Transformations et travaux**->**Create or Update Transform** (Créer ou mettre à jour une transformation).

La méthode de requête HTTP **PUT** se présente ainsi :

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Sélectionnez l’onglet **Corps** et remplacez le corps par le code JSON que vous avez [défini précédemment](#define-a-custom-preset). Pour que la plateforme Media Services applique la transformation au fichier vidéo ou audio spécifié, vous devez soumettre un travail relevant de cette transformation.

Sélectionnez **Envoyer**. 

Pour que la plateforme Media Services applique la transformation au fichier vidéo ou audio spécifié, vous devez soumettre un travail relevant de cette transformation. Pour obtenir un exemple complet illustrant la soumission d’un travail relevant d’une transformation, consultez le [Didacticiel : Encoder un fichier distant basé sur une URL et streamer la vidéo - REST](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Étapes suivantes

Consultez les [autres opérations REST](https://docs.microsoft.com/rest/api/media/).
