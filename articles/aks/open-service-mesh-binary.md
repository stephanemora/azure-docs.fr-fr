---
title: Télécharger la bibliothèque de client OSM
description: Télécharger la bibliothèque de client Open Service Mesh (OSM)
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 128d0371e65d06e413f503094b56522c67ea83c0
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123441006"
---
# <a name="download-the-open-service-mesh-osm-client-library"></a>Télécharger la bibliothèque de client Open Service Mesh (OSM)
Cet article explique la procédure de téléchargement de la bibliothèque de client OSM à utiliser pour faire fonctionner et configurer le module complémentaire OSM pour AKS.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-windows.md)]

::: zone-end

> [!WARNING]
> N’essayez pas d’installer OSM à partir du fichier binaire avec `osm install`. Cela aboutira à une installation d’OSM qui n’est pas intégrée en tant que module complémentaire pour AKS.