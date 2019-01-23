---
title: Clouds et régions dans lesquels Azure Media Services v3 est disponible | Microsoft Docs
description: Cet article traite des clouds Azure et régions dans lesquels Azure Media Services v3 est disponible.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/11/2019
ms.author: juliako
ms.openlocfilehash: 8eb49010d89c3039f46e5c84cd305b7d0b5ca025
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54306965"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Clouds et régions dans lesquels Azure Media Services v3 existe

Azure Media Services v3 est disponible via le manifeste Azure Resource Manager dans Azure global, Azure Government, Azure Germany, Azure China 21Vianet. Cela étant, toutes les fonctionnalités de Media Services ne sont pas disponibles dans l'ensemble des clouds Azure. Ce document présente la disponibilité des principaux composants Media Services v3.

## <a name="feature-availability-in-azure-clouds"></a>Disponibilité des fonctionnalités dans les clouds Azure

| Fonctionnalité|Régions Azure à l'échelle internationale | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | Disponible | Non disponible | Non disponible | Non disponible |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Disponible | Non disponible | Non disponible | Non disponible |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Disponible | Non disponible | Non disponible | Non disponible |
| [StandardEncoderPreset](encoding-concept.md) | Disponible | Disponible | Disponible | Disponible |
| [LiveEvents](live-streaming-overview.md) | Disponible | Disponible | Disponible | Disponible |
| [StreamingEndpoints](streaming-endpoint-concept.md) | Disponible | Disponible | Disponible | Disponible |

## <a name="regions"></a>Régions 

S'il vous faut fournir le paramètre **location**, vous devez fournir le nom de code de la région comme valeur **location**. Pour obtenir le nom de code de la région dans laquelle se trouve votre compte et vers laquelle votre appel doit être routé, vous pouvez exécuter la ligne suivante dans [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) :

```bash
az account list-locations
```

Une fois que vous avez exécuté la ligne ci-dessus, vous obtenez une liste de toutes les régions Azure. Accédez à la région Azure qui présente la valeur *displayName* que vous recherchez et utilisez sa valeur *name* pour le paramètre **location**.

Par exemple, pour la région Azure West US 2 (USA Ouest 2) affichée ci-dessous, vous allez utiliser « westus2 » pour le paramètre **location**.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de Media Services v3](media-services-overview.md)
