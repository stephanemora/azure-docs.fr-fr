---
title: Télécharger la bibliothèque de client OSM
description: Télécharger la bibliothèque de client Open Service Mesh (OSM)
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 83eec8682291a74bc9b23bf4ff1326cee7134e7f
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534947"
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

> [!NOTE]
> Il est recommandé de configurer l’interface CLI OSM pour [personnaliser l’expérience du module complémentaire AKS OSM](/open-service-mesh-customize-addon-experience.md) après l’installation du fichier binaire, avant d’utiliser l’interface CLI OSM. 