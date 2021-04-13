---
title: Rogner des fichiers vidéo avec Media Services - .NET | Microsoft Docs
description: Le rognage consiste à sélectionner une fenêtre rectangulaire dans l’image vidéo et à encoder uniquement les pixels dans cette fenêtre. Cette rubrique montre comment rogner des fichiers vidéo avec Media Services à l’aide de .NET.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 43d0e1e3b8c0eaa37a3b09557b333c3abafe8b63
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572210"
---
# <a name="how-to-crop-video-files-with-media-services---net"></a>Rogner des fichiers vidéo avec Media Services - .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Vous pouvez utiliser Media Services pour rogner une vidéo d’entrée. Le rognage consiste à sélectionner une fenêtre rectangulaire dans l’image vidéo et à encoder uniquement les pixels dans cette fenêtre. Le schéma suivant permet d’illustrer le processus.

## <a name="pre-processing-stage"></a>Étape de prétraitement

Le rognage étant une étape de prétraitement, les *paramètres de rognage* de la présélection d’encodage s’appliquent à la vidéo *d’entrée*. L’encodage s’effectue à un stade ultérieur, et les paramètres de largeur/hauteur s’appliquent à la vidéo *prétraitée* et non à la vidéo d’origine. Lorsque vous concevez votre présélection, procédez comme suit :

1. Sélectionner les paramètres de rognage en fonction de la vidéo d’entrée d’origine
1. Sélectionnez vos paramètres d’encodage en fonction de la vidéo rognée.

> [!WARNING]
> Si vos paramètres d’encodage ne correspondent pas à la vidéo rognée, le résultat ne répondra pas à vos attentes.

Par exemple, votre vidéo d’entrée présente une résolution de 1920 x 1080 pixels (proportions 16:9), avec des barres noires (pillarbox) à gauche et à droite, de manière à ce que seule une fenêtre de 4:3 ou 1 440 x 1 080 pixels puisse contenir une vidéo active. Vous pouvez rogner les barres noires et encoder la zone 1 440 x 1 080.

## <a name="transform-code"></a>Transformer le code

L’extrait de code suivant montre comment écrire une transformation dans .NET pour rogner des vidéos.  Le code suppose que vous disposez d’un fichier local à utiliser.

- Left est l’emplacement le plus à gauche du rognage.
- Top est l’emplacement le plus haut du rognage.
- Width est la largeur finale du rognage.
- Height est la hauteur finale du rognage.

```dotnet
var preset = new StandardEncoderPreset

    {

        Filters = new Filters

        {                   

            Crop = new Rectangle

            {

                Left = "200",

                Top = "200",

                Width = "1280",

                Height = "720"

            }

        },

        Codecs =

        {

            new AacAudio(),

            new H264Video()

            {

                Layers =

                {                           

                    new H264Layer

                    {

                        Bitrate = 1000000,

                        Width = "1280",

                        Height = "720"

                    }

                }

            }

        },

        Formats =

        {

            new Mp4Format

            {

                FilenamePattern = "{Basename}_{Bitrate}{Extension}"

            }

        }

    }

```
