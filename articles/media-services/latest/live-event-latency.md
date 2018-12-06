---
title: Latence LiveEvent dans Azure Media Services | Microsoft Docs
description: Cette rubrique donne une vue d’ensemble de la latence LiveEvent et montre comment définir une faible latence.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: a74f2e53127b506f42ff49018c3df2985396646d
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52619812"
---
# <a name="liveevent-latency-in-media-services"></a>Latence LiveEvent dans Media Services

Cet article explique comment définir une faible latence sur un événement **LiveEvent**. Il traite également des résultats généralement obtenus avec des paramètres de faible latence sur différents lecteurs. Les résultats varient en fonction de la latence réseau et du CDN. 

Pour utiliser la nouvelle fonction **LowLatency**, définissez le paramètre **StreamOptionsFlag** sur **LowLatency** dans l’événement **LiveEvent**. Une fois que le flux de données est opérationnel et actif, vous pouvez ouvrir la page de démonstration du [lecteur multimédia Azure](http://ampdemo.azureedge.net/) et configurez les options de lecture afin d’utiliser le profil heuristique à faible latence (« Low Latency Heuristics Profile »).

L’exemple .NET suivant montre comment définir **LowLatency** sur l’événement **LiveEvent** :

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Basic to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second GOP size instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

Pour voir l’exemple complet : [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="pass-through-liveevents-latency"></a>Latence LiveEvent pass-through

Le tableau suivant illustre les résultats classiques de latence (lorsque l’indicateur LowLatency est activé) dans Media Services, mesurée entre le moment où le flux de contribution atteint le service et celui où le lecteur peut demander la lecture.

||GOP 2 s à faible latence|GOP 1 s à faible latence|
|---|---|---|
|DASH dans AMP|10 s|8 s|
|HLS sur lecteur iOS natif|14 s|10 s|

> [!NOTE]
> La latence de bout en bout peut varier en fonction des conditions du réseau local ou en introduisant une couche de mise en cache d’un réseau de distribution de contenu. Faites des tests spécifiquement dans vos configurations.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du streaming en direct](live-streaming-overview.md)
- [Didacticiel sur le streaming en direct](stream-live-tutorial-with-api.md)

