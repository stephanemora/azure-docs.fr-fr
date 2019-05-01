---
title: Encoder de transformation personnalisée à l’aide de Media Services v3 CLI - Azure | Microsoft Docs
description: Cette rubrique montre comment utiliser Azure Media Services v3 pour coder une transformation personnalisée à l’aide de CLI.
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
ms.openlocfilehash: b0329c8c0bfa56e5ae36c5f4223edca91e6510a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733111"
---
# <a name="how-to-encode-with-a-custom-transform---cli"></a>Comment encoder avec une transformation personnalisée - CLI

Lors de l’encodage avec Azure Media Services, vous pouvez démarrer rapidement avec l’une des présélections intégrées recommandées, selon les meilleures pratiques du secteur, comme illustré dans le [diffusion en continu de fichiers](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) Guide de démarrage rapide. Vous pouvez également créer un paramètre prédéfini pour cibler vos exigences de scénario ou un périphérique spécifiques.

## <a name="considerations"></a>Considérations

Lorsque vous créez des paramètres prédéfinis personnalisés, les considérations suivantes s’appliquent :

* Toutes les valeurs de hauteur et la largeur du contenu de AVC doivent être un multiple de 4.
* Dans Azure Media Services v3, tous les débits binaires de codage sont en bits par seconde. Cela diffère des présélections avec nos API v2, utiliser des kilobits par seconde en tant que l’unité. Par exemple, si la vitesse de transmission dans v2 a été spécifié en tant que 128 (kilobits par seconde), dans v3 il serait défini à 128000 (bits/seconde).

## <a name="prerequisites"></a>Conditions préalables 

[Créer un compte Media Services](create-account-cli-how-to.md). <br/>Veillez à mémoriser le nom du groupe de ressources et le nom du compte Media Services. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Définir une présélection personnalisée

L’exemple suivant définit le corps de la demande d’une nouvelle transformation. Nous définissons un ensemble de sorties que nous voulons être générée lorsque cette transformation est utilisée. 

Dans cet exemple, nous avons tout d’abord ajouter une couche de AacAudio pour l’encodage audio et deux couches H264Video pour l’encodage vidéo. Dans les couches vidéo, nous attribuons les étiquettes afin qu’ils peuvent être utilisés dans les noms de fichiers de sortie. Ensuite, nous voulons la sortie pour inclure également les miniatures. Dans l’exemple ci-dessous, nous spécifions les images au format PNG, généré à 50 % de la résolution de la vidéo d’entrée et aux trois horodatages - {25 %, 50 %, 75} de la longueur de la vidéo d’entrée. Enfin, nous spécifions le format pour les fichiers de sortie - un pour la vidéo + audio et l’autre pour les miniatures. Étant donné que nous avons plusieurs H264Layers, nous devons utiliser des macros qui produisent des noms uniques par couche. Nous pouvons utiliser un `{Label}` ou `{Bitrate}` macro, l’exemple montre l’ancienne base de données.

Nous allons enregistrer cette transformation dans un fichier. Dans cet exemple, nous nommez le fichier `customPreset.json`. 

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

Dans cet exemple, nous créons un **transformer** qui repose sur la présélection personnalisée définie précédemment. Lorsque vous créez une transformation, vous devez tout d’abord vérifier si celle-ci existe. Si la transformation existe, la réutiliser. Ce qui suit `show` commande retourne le `customTransformName` transformer si elle existe :

```cli
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

La commande CLI suivante crée la transformation en fonction de la présélection personnalisée (définie précédemment). 

```cli
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Media Services appliquer la transformation au spécifié vidéo ou audio, vous devez soumettre une tâche de cette transformation. Pour obtenir un exemple complet qui montre comment soumettre un travail sous une transformation, consultez [Guide de démarrage rapide : Stream fichiers vidéo - CLI](stream-files-cli-quickstart.md).

## <a name="see-also"></a>Voir aussi

[Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
