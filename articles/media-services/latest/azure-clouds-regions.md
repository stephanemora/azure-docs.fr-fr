---
title: Clouds et régions dans lesquels Azure Media Services v3 est disponible
description: Cet article traite des clouds Azure et régions dans lesquels Azure Media Services v3 est disponible.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: 9c54482dc188141c4fc697bd43628b81e0ddc29f
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91597126"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Clouds et régions dans lesquels Azure Media Services v3 existe

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

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

## <a name="feature-availability-in-preview"></a>Disponibilité des fonctionnalités en préversion

[LiveTranscription](live-transcription.md) est disponible dans les régions suivantes :

- Asie Sud-Est
- Europe Ouest
- Europe Nord
- USA Est
- USA Centre
- États-Unis - partie centrale méridionale
- USA Ouest 2
- Brésil Sud

## <a name="regionsgeographieslocations"></a>Régions/zones géographiques/emplacements

[Régions dans lesquelles le service Azure Media Services est déployé](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Nom de code de la région

S'il vous faut fournir le paramètre **location**, vous devez fournir le nom de code de la région comme valeur **location**. Pour obtenir le nom de code de la région dans laquelle se trouve votre compte et vers laquelle votre appel doit être routé, vous pouvez exécuter la ligne suivante dans l’[interface de ligne de commande Azure](/cli/azure/?view=azure-cli-latest).

```azurecli-interactive
az account list-locations
```

Une fois que vous avez exécuté la ligne ci-dessus, vous obtenez une liste de toutes les régions Azure. Accédez à la région Azure qui présente la valeur *displayName* que vous recherchez et utilisez sa valeur *name* pour le paramètre **location**.

Par exemple, pour la région Azure USA Ouest 2 affichée ci-dessous, vous allez utiliser « westus2 » pour le paramètre **location**.

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

## <a name="endpoints"></a>Points de terminaison  

Il est important de connaître les points de terminaison suivants lors de la connexion à des comptes Media Services à partir de différents clouds Azure nationaux.

### <a name="global-azure"></a>Azure global

| Service | Point de terminaison |
| ------- | -------- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Authentification | `https://login.microsoftonline.com/` |
| Audience du jeton | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

| Service | Point de terminaison |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Authentification | `https://login.microsoftonline.us/` |
| Audience du jeton | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure Germany

| Service | Point de terminaison |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Authentification | `https://login.microsoftonline.de/` |
| Audience du jeton | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

| Service | Point de terminaison |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Authentification | `https://login.chinacloudapi.cn/` |
| Audience du jeton |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Voir aussi

* [Régions Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Zones géographiques Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Emplacements Azure](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de Media Services v3](media-services-overview.md)
