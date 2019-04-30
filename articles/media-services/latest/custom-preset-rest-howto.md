---
title: Encoder de transformation personnalisée à l’aide de Media Services v3 REST - Azure | Microsoft Docs
description: Cette rubrique montre comment utiliser Azure Media Services v3 pour coder une transformation personnalisée à l’aide de REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: a9de15530981e14e664df605cb3274c9e754ef0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733026"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Comment encoder avec une transformation personnalisée - REST

Lors de l’encodage avec Azure Media Services, vous pouvez démarrer rapidement avec l’une des présélections intégrées recommandées, selon les meilleures pratiques du secteur, comme illustré dans le [diffusion en continu de fichiers](stream-files-tutorial-with-rest.md#create-a-transform) didacticiel. Vous pouvez également créer un paramètre prédéfini pour cibler vos exigences de scénario ou un périphérique spécifiques.

## <a name="considerations"></a>Considérations

Lorsque vous créez des paramètres prédéfinis personnalisés, les considérations suivantes s’appliquent :

* Toutes les valeurs de hauteur et la largeur du contenu de AVC doivent être un multiple de 4.
* Dans Azure Media Services v3, tous les débits binaires de codage sont en bits par seconde. Cela diffère des présélections avec nos API v2, utiliser des kilobits par seconde en tant que l’unité. Par exemple, si la vitesse de transmission dans v2 a été spécifié en tant que 128 (kilobits par seconde), dans v3 il serait défini à 128000 (bits/seconde).

## <a name="prerequisites"></a>Conditions préalables 

- [Créer un compte Media Services](create-account-cli-how-to.md). <br/>Veillez à mémoriser le nom du groupe de ressources et le nom du compte Media Services. 
- [Configurer Postman pour les appels d’API REST Azure Media Services](media-rest-apis-with-postman.md).<br/>Suivez la dernière étape de la rubrique [Obtenir un jeton Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Définir une présélection personnalisée

L’exemple suivant définit le corps de la demande d’une nouvelle transformation. Nous définissons un ensemble de sorties que nous voulons être générée lorsque cette transformation est utilisée. 

Dans cet exemple, nous avons tout d’abord ajouter une couche de AacAudio pour l’encodage audio et deux couches H264Video pour l’encodage vidéo. Dans les couches vidéo, nous attribuons les étiquettes afin qu’ils peuvent être utilisés dans les noms de fichiers de sortie. Ensuite, nous voulons la sortie pour inclure également les miniatures. Dans l’exemple ci-dessous, nous spécifions les images au format PNG, généré à 50 % de la résolution de la vidéo d’entrée et aux trois horodatages - {25 %, 50 %, 75} de la longueur de la vidéo d’entrée. Enfin, nous spécifions le format pour les fichiers de sortie - un pour la vidéo + audio et l’autre pour les miniatures. Étant donné que nous avons plusieurs H264Layers, nous devons utiliser des macros qui produisent des noms uniques par couche. Nous pouvons utiliser un `{Label}` ou `{Bitrate}` macro, l’exemple montre l’ancienne base de données.

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
                            "filenamePattern": "Video-{Basename}-{Label}{Extension}",
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

Dans cet exemple, nous créons un **transformer** qui repose sur la présélection personnalisée définie précédemment. Lorsque vous créez une transformation, vous devez tout d’abord utiliser [obtenir](https://docs.microsoft.com/rest/api/media/transforms/get) pour vérifier s’il en existe déjà. Si la transformation existe, la réutiliser. 

Dans la collection de Postman que vous avez téléchargé, sélectionnez **transforme et travaux**->**Create ou Update transformer**.

La méthode de requête HTTP **PUT** se présente ainsi :

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Sélectionnez le **corps** onglet et remplacez le corps avec le code json de code vous [défini précédemment](#define-a-custom-preset). Media Services appliquer la transformation au spécifié vidéo ou audio, vous devez soumettre une tâche de cette transformation.

Sélectionnez **Envoyer**. 

Media Services appliquer la transformation au spécifié vidéo ou audio, vous devez soumettre une tâche de cette transformation. Pour obtenir un exemple complet qui montre comment soumettre un travail sous une transformation, consultez [didacticiel : Fichiers vidéo - REST de Stream](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Étapes suivantes

Consultez [autres opérations REST](https://docs.microsoft.com/rest/api/media/)
