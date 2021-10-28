---
title: Télécharger la bibliothèque de client OSM
description: Télécharger la bibliothèque de client Open Service Mesh (OSM)
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: fb3619ed95f0636ae83829e1ee1c818545dc86c9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227337"
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
> Il est recommandé de configurer l’interface CLI OSM pour [personnaliser l’expérience du module complémentaire AKS OSM](./open-service-mesh-customize-add-on-experience.md) après l’installation du fichier binaire, avant d’utiliser l’interface CLI OSM.